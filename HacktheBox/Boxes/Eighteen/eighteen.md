# Eighteen

<img width="100" height="443" alt="image" src="https://github.com/kittykrat/crispy-giggle/blob/main/HacktheBox/Boxes/Eighteen/Images/etlogo.png" />

## Linux - Easy

- [**User Flag**](https://github.com/kittykrat/HacktheBox/edit/main/Boxes/Eighteen/eighteen.md#user-flag)
- [**Root Flag**](https://github.com/kittykrat/HacktheBox/edit/main/Boxes/Eighteen/eighteen.md#root-flag)
- [**Key Takeaways & Lessons Learned**](https://github.com/kittykrat/HacktheBox/edit/main/Boxes/Eighteen/eighteen.md#key-takeaways--lessons-learned)

## Machine Information:

> As is common in real life Windows penetration tests, you will start the Eighteen box with credentials for the following account: `kevin` / `iNa2we6haRj2gaw!`

## User Flag

First, we run an Nmap scan on our target IP to identify which TCP ports are open.

`sudo nmap -sC -sV -p- -T4 --min-rate 1000 -oN nmap_full.txt 10.129.XXX.XXX`

The results from the scan:

<img width="400" alt="{2074FF7E-6B53-4046-8B02-95854920F783}" src="https://github.com/user-attachments/assets/957b8564-884e-44d4-b735-263c8979ee67" />


```
PORT     STATE SERVICE  REASON          VERSION
80/tcp   open  http     syn-ack         Microsoft IIS httpd 10.0
1433/tcp open  ms-sql-s syn-ack ttl 127 Microsoft SQL Server 2022 16.00.1000.00; RCO+
```

***

### Results (Relevant Ports):

| PORT       | STATE           | SERVICE  | VERSION  |
|:-------------:|:-------------:|:-------------:|:-------------:|
| 80/tcp | open | http | Microsoft IIS httpd 10.0 |
| 1433/tcp | open | ms-sql-s | Microsoft SQL Server 2022 16.00.1000.00; RCO+ |

- We then add the target IP to /etc/hosts so that we may access it by hostname:

`echo "10.129.xxx.xxx eighteen.htb" | sudo tee -a /etc/hosts`

***

- Visiting the website we see:

<img width="400" alt="{6143BD5F-F05F-49F0-A416-B1DBE926699B}" src="https://github.com/user-attachments/assets/7c91c557-3225-4de9-ba6f-6758b51fd0b2" />

- Taking a look at the page, manually decoding the Flask Cookie

- Running NetExec with MSSQL Priv module which lets us know we can impersonate, switching to mssqlclient

- Impersonating appdev, which can read the financial_planner table

- Converting the PBKDF2 hash to the Django format so we can try to crack it

- Using NXC to run RID BRUTE through MSSQL and get other users to spray the password with

- Using Evil-WinRM to access the box as Adam.Scott then poke at the webserver files, nothing here

- Getting the Windows Patch Level, noticing windows 2025 and searching exploits to find BadSuccessor

- Setting up Chisel so we can tunnel back to our box to run the badsuccessor module with nxc

- Looking at NXC Issues to see the support for BadSuccessor is still a PR, installing the special branch with uv

- Setting our system time to the time on the webserver based upon the Date Header from Curl

- Running BadSuccessor getting the NTLM hash of administrator and using psexec to get on the box

## Root Flag


***

## Key Takeaways & Lessons Learned


