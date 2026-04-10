# Linux Authentication Concepts

#### PAM (Pluggable Authentication Modules)

- **PAM (Pluggable Authentication Modules)** is a framework used by "PAM-aware" applications (like `login`, `su`, `sshd`, `sudo`) to perform authentication and related account/session tasks.
- When a PAM-aware app starts, it attaches to the **PAM API** and reads PAM configuration to decide **which modules to run** and **how to handle success/failure.**

```
**Application** → calls **PAM** → PAM runs a **stack of modules** (rules) → result is returned to the application.

```
