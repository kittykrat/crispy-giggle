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



## Root Flag


***

## Key Takeaways & Lessons Learned


