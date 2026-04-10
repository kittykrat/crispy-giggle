# Windows Authentication Concepts

#### NTLM

- **NTLM** is a family of legacy Windows Authentication protocols implemented in Windows via `Msv1_0.dll`.
- The "family" includes **LMv1/LMv2** and **NTLMv1/NTLMv2**.
- NTLM proves identity using a **challenge-response** approach (the password itself is not sent directly).

#### How NTLM authentication works

- NTLM authentication centers on the idea
  > "Prove you know the password" **without** sending it in cleartext.

- #### Simplified flow
  - ```
    Client → Server: "I'm user X"
    Server → Client: Challenge (random data)
    Client → Server: Response (computed from challenge + secret derived from password)
    Server → DC or local SAM: Validate response and issue/deny token.
    ``` 
