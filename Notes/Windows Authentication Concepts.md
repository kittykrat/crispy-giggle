# Windows Authentication Concepts

#### NTLM

- **NTLM** is a family of legacy Windows Authentication protocols implemented in Windows via `Msv1_0.dll`.
- The "family" includes **LMv1/LMv2** and **NTLMv1/NTLMv2**.
- NTLM proves identity using a **challenge-response** approach (the password itself is not sent directly).

#### How NTLM authentication works

- NTLM authentication centers on the idea
  > "Prove you know the password" **without** sending it in cleartext.

#### Simplified flow

  - ```
    Client → Server: "I'm user X"
    Server → Client: Challenge (random data)
    Client → Server: Response (computed from challenge + secret derived from password)
    Server → DC or local SAM: Validate response and issue/deny token.
    ``` 

#### What the resource server must do (domain vs local)

- When NTLM is used, the **resource server** has to validate identity by doing **one** of these:
  - **Domain account:** contact the **domain authentication service (DC)** for validation.
  - **Local account:** check the **local account database** (local machine).
 
#### Where NTLM is still used today (common cases)

Even though it's legacy, NTLM remains in use because of compatibility and non-domain scenarios:
- **Workgroup systems** (no AD / no Kerberos) require NTLM for Windows authentication.
- **Local logons** on non-domain controllers still use NTLM mechanisms.
- In an AD environment, **Kerberos v5 is preferred**, but some Microsoft/non-Microsoft apps still fall back to NTLM.

#### Why reducing NTLM requires discovery first

- Microsoft's guidance emphasizes: you can't reduce NTLM without knowing **which apps/services still depend on it,** and then applying strategies to move to other protocols (typically Kerberos).
- To help with this, Microsoft has added **tools/settings to discover NTLM usage** so you can selectively restrict it.
