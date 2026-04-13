# Shadowbait

<img width="100" alt="image" src="https://github.com/ty-bradley/HacktheBox/blob/main/Sherlocks/ShadowBait/Images/sblogo.png" />

## DFIR - Very Easy

> ### Sherlock Scenario
> Steven, a junior consultant at a mid-sized firm recently downloaded a document from an external source, leading to a potential security incident. An attacker may have gained unauthorized access to a Windows machine, leaving behind traces of their activities. Your task is to examine the available disk artifacts to identify any suspicious behavior and help secure the system.


- We download the required `ShadowBait.zip` containing 3 files and a directory:
  - 2025-06-12T01_37_33_0689927_ConsoleLog.txt
  - 2025-06-12T01_37_33_0689927_CopyLog.csv
  - 2025-06-12T01_37_33_0689927_SkipLog.csv.csv
  - G

***

## Tasks:

### Task 1:
#### What is the name of the malicious document used in phishing, which facilitated initial access for the attacker?



- **Flag for task:**
```

```

***

### Task 2:
#### What was the full link from which the malicious document was downloaded?

- **Flag for task:**
```

```

***

### Task 3:
#### The document downloaded a script, which acted as a stager and downloaded another payload, providing the attacker with hands-on remote access. When was this script downloaded?

- **Flag for task:**
```

```

***

### Task 4:
#### What is the full path of the final payload which provided remote access to the attacker?

- **Flag for task:**
```

```

***

### Task 5:
#### Which port was used for C2 communication by the payload?

- **Flag for task:**
```

```

***

### Task 6:
#### The threat actor utilized a file created and used by Steven even before the attack. This file allowed the attacker to authenticate as user "Samy", abusing DPAPI to grab credentials of another user on the same machine. What is the full path of this file?



- **Flag for task:**
```

```

***

### Task 7:
#### What is the full command used to grab credentials from the "Samy" account?

- **Flag for task:**
```

```

***

### Task 8:
#### The attacker downloaded a tool from their internal staging server to laterally move and gain remote access as "Samy" user account. What is the full command used to download the tool?

- **Flag for task:**
```

```

***

### Task 9:
#### What is the password for the user account "Samy", used by the attacker to gain a remote shell?

- **Flag for task:**
```

```

***

### Task 10:
#### After gaining access as Samy, the attacker downloaded a script to check privileges for the account. What is the name of the script?

- **Flag for task:**
```

```

***

### Task 11:
#### The attacker exploited a Windows process to gain an elevated remote shell. What is the PID of this process?

- **Flag for task:**
```

```

***

### Task 12:
#### Which port was used for remote access with escalated privileges?

- **Flag for task:**
```

```

***

### Task 13:
#### The attacker enabled persistence mechanisms for a backdoor executable. What is the full path of the file?

- **Flag for task:**
```

```

***

### Task 14:
#### The attacker also abused Windows shortcuts and placed a rogue shortcut file pointing to the malicious backdoor. What is the shortcut file name?

- **Flag for task:**
```

```

***

### Task 15:
#### What is the full path of the script that created the shortcut persistence?

- **Flag for task:**
```

```

***
