# Windows Authentication Concepts

**NTLM** _(NT LAN Manager)_ is a suite of Microsoft security protocols used for authenticating user identity and ensuring data integrity in Windows environments.
It operates via a challenge-response mechanism, acting as an early Single Sign-On (SSO) tool, but is now considered legacy, having been largely replaced by Kerberos.

NTLM authentication is a family of authentication protocols that are encompassed in the Windows Msv1_0.dll. The NTLM authentication protocols include LAN Manager version 1 and 2, and NTLM version 1 and 2.

The NTLM authentication protocols authenticate users and computers based on a challenge response mechanism that proves to a server or domain controller that a user knows the password associated with an account.

When the NTLM protocol is used, a resource server must take one of the following actions to verify the identity of a computer or user whenever a new access token is needed:

  - Contact a domain authentication service on the domain controller for the computer's or user's account domain, if the account is a domain account.
  - Look up the computer's or user's account in the local account database, if the account is a local account.
