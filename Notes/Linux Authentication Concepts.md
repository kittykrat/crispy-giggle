# Linux Authentication Concepts

## PAM (Pluggable Authentication Modules)

- **PAM (Pluggable Authentication Modules)** is a framework used by "PAM-aware" applications (like `login`, `su`, `sshd`, `sudo`) to perform authentication and related account/session tasks.
- When a PAM-aware app starts, it attaches to the **PAM API** and reads PAM configuration to decide **which modules to run** and **how to handle success/failure.**

```
**Application** → calls **PAM** → PAM runs a **stack of modules** (rules) → result is returned to the application.
```

***

## PAM within a Cybersecurity context

- PAM defines authentication, authorization, and session policy for Linux services.
- Misconfiguration can bypass MFA or password checks.
- Attackers may modify PAM configs for persistence or privilege escalation.
- PAM controls often determine whether logs, lockouts, or restrictions apply.


***

## PAM Configuration Files: pam.conf and pam.d

### Where PAM reads confriugation from:

- PAM reads either:
  - `/etc/pam.conf` (single file), **or**
  - individual service files in `/etc/pam.d/` (preferred).
 - If `/etc/pam.d/` exists, PAM **ignores /etc/pam.conf**.

### Vendor config locations (precedence)

- Vendor-supplied defaults may exist in:
  - `/usr/lib/pam.d/`
  - `/usr/share/pam/pam.d/`
- Files in `/etc/pam.d/` **override** vendor files of the same name.

***

### What these files contain

- PAM config files contain **rules** that define:
  1. **Which module** runs (e.g., `pam_unix.so`, `pam_sss.so`, etc.)
  2. **Which stage** it runs in (`auth`, `account`, `password`, `session`)
  3. **What to do** if it succeeds/fails (the _control_ field).

***

### Rule syntax

- **In /etc/pam.conf**

Each rule is:

```
service type control module-path module-arguments
```

- **In /etc/pam.d/<service>**

Same idea, but **no "service" column** (the filename _is_ the service name):

```
type control module-path module-arguments
```

**Example concept:** `/etc/pam.d/login` contains rules for the `login` service.

***

**Service field**
- The **service name** is usually the application name (e.g., `login`, `su`).
- There is a special service-name `**other**` used for default rules.
- Only rules matching the current service (or other as fallback) apply.

***

### The Four PAM "Types" (management groups)

These are the four stages of the authentication lifecycle:

- **auth**
  - Proves identity ("are you who you say you are?") by prompting for password/OTP/etc.
  - Can also grant credentials/privileges (e.g., group membership) through credential handling.
 
- **account**
  - Non-password checks ("are you allowed to log in?") like time restrictions, resource limits, login location constraints.
 
- **password**
  - Handles **updating** authentication tokens (changing passwords, enforcing password policy).
 
- **session**
  - Actions **before/after** access is granted: logging, mounting home dirs, setting up environment, etc.

> auth = verify, account = allowed, password = change, session = setup/teardown

***

### PAM runs multiple rules in order

- A major feature of PAM is that rules can be **stacked** (multiple modules combined for one service/type).

This is how you get patterns like:

  - local users first, then LDAP/SSSD
  - password + MFA
  - deny/allow rules + logging rules

***

### Control Field (how failures/success are handled)

- The **control** field tells PAM how to interpret the module's result.

**Simple (common) control keywords**

- **required**
  
  - If it fails → overall failure **eventually**, but PAM still runs the rest of the stack first.
 
- **requisite**
  
  - Like required, but **fails immediately** and returns to the application/upper stack.
 
- **sufficient**
  
  - If it succeeds and no earlier required failed → **success immediately**, stop processing more modules.
  - If it fails → ignore and continue.
 
- **optional**
  
  - Only matters if it's the **only** module in that service+type stack.
 
- **include**
  
  - Include all lines of a given type from another config file (named as argument).
 
- **substack**
  
  - Like `include`, but with different "jump/terminate" behavior: it won't skip the rest of the _entire_ stack, only the substack is treated specially.

***

### Advanced control syntax

- PAM supports bracketed logic: `[value=action]`

- Used mainly in complex or vendor-supplied stacks.

- Not commonly written by admins, but useful to recognize during audits.

***

### Equivalent mappings

- The simple control flags are shorthand for equivalent bracket syntax; useful when reading complex stacks.

***

## Module paths & arguments

### Module path

- Can be an absolute path (`/…`) or relative to default module locations:
  -` /lib/security/` or `/lib64/security/` (architecture-dependent).

***

### Module arguments

- Space-separated options passed to a module to change its behavior.
If you need spaces inside one argument, you can wrap it in **square brackets** `[...]` (and escape `]` as `\]`).

***

### Logging & errors

- Misformatted lines generally cause PAM to **fail authentication** (fail-safe) and write an error via syslog.

***

### One extra detail worth noting

If you prefix a type with - (e.g., -auth), PAM will **not log** to syslog if a module cannot be loaded because it’s missing—useful when a module is optional/not always installed.

***

**Quick “How to Read a PAM Line”**

- When you see a line like:

  - ```
    auth  required  pam_unix.so  try_first_passShow more lines
    ```

- Interpret it as:

  - **auth:** identity verification stage
  - **required:** must succeed (but may continue evaluating other modules before returning failure)
  - **pam_unix.so:** module being invoked (from default module paths)
  - **try_first_pass:** module option/argument controlling behavior

***

## Files to monitor (Incident Response / Detection)

```
/etc/pam.d/*
/etc/pam.conf
/lib/security/*.so
/lib64/security/*.so
```

***

## Authentication Logs

- Debian/Ubuntu: `/var/log/auth.log`
- RHEL/CentOS/Rocky: `/var/log/secure`
- systemd-based systems: `journalctl (sshd, sudo, login)`

***

## Common PAM security pitfalls

- 'sufficient' modules placed before required controls (can bypass MFA)
- Missing account checks (auth succeeds but should not be allowed)
- Vendor PAM updates overridden unintentionally by `/etc/pam.d`
- Silent failures when '-' prefix suppresses logging.

***

> ### Cheat Sheet
>
> - **PAM** = authentication policy engine (per-service rules under /etc/pam.d/).
> - **NSS** = where user/group lookup comes from (files, LDAP, SSSD, etc.)—SSSD often provides the NSS side.
> - **SSSD** = enterprise client for identity/auth (LDAP, Kerberos, FreeIPA, AD) + caching + NSS/PAM integration.
> - **Kerberos** = ticket-based authentication (stronger than sending passwords repeatedly).
> - **LDAP/OpenLDAP** = directory of identities and attributes; often paired with Kerberos for auth.
