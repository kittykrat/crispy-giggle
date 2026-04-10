# Linux Authentication Concepts

#### PAM (Pluggable Authentication Modules)

- **PAM (Pluggable Authentication Modules)** is a framework used by "PAM-aware" applications (like `login`, `su`, `sshd`, `sudo`) to perform authentication and related account/session tasks.
- When a PAM-aware app starts, it attaches to the **PAM API** and reads PAM configuration to decide **which modules to run** and **how to handle success/failure.**

```
**Application** → calls **PAM** → PAM runs a **stack of modules** (rules) → result is returned to the application.
```

***

### PAM Configuration Files: pam.conf and pam.d


Phase 1 (1–2 days): Local authentication basics

/etc/passwd, /etc/shadow, /etc/group fundamentals [windowsforum.com]
PAM concepts and config layout (/etc/pam.d) [man7.org], [man.archlinux.org]

Phase 2 (3–7 days): How Linux plugs into centralized identity

SSSD concepts + providers (LDAP/Kerberos/AD/IPA) [sssd.io], [github.com]
Ubuntu SSSD + LDAP + Kerberos hands-on guide [ubuntu.com]
Red Hat SSSD + TLS guidance (security considerations) [docs.redhat.com]

Phase 3 (ongoing): Kerberos & directory deepening

MIT Kerberos docs for the canonical reference [web.mit.edu]
OpenLDAP admin guide for directory fundamentals [openldap.org]
FreeIPA docs if your environment is Linux-domain-centric [github.com], [freeipa.org]


### Cheat Sheet

**PAM** = authentication policy engine (per-service rules under /etc/pam.d/). [man7.org], [man.archlinux.org]
**NSS** = where user/group lookup comes from (files, LDAP, SSSD, etc.)—SSSD often provides the NSS side. [github.com], [sssd.io]
**SSSD** = enterprise client for identity/auth (LDAP, Kerberos, FreeIPA, AD) + caching + NSS/PAM integration. [sssd.io], [github.com]
**Kerberos** = ticket-based authentication (stronger than sending passwords repeatedly). [docs.redhat.com], [web.mit.edu]
**LDAP/OpenLDAP** = directory of identities and attributes; often paired with Kerberos for auth. [openldap.org], [ubuntu.com]
