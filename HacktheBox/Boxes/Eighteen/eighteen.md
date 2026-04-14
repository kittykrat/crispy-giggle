# Eighteen

<img width="100" height="443" alt="image" src="https://github.com/kittykrat/crispy-giggle/blob/main/HacktheBox/Boxes/Eighteen/Images/etlogo.png" />

## Linux - Easy

- [**User Flag**](https://github.com/kittykrat/HacktheBox/edit/main/Boxes/Eighteen/eighteen.md#user-flag)
- [**Root Flag**](https://github.com/kittykrat/HacktheBox/edit/main/Boxes/Eighteen/eighteen.md#root-flag)
- [**Key Takeaways & Lessons Learned**](https://github.com/kittykrat/HacktheBox/edit/main/Boxes/Eighteen/eighteen.md#key-takeaways--lessons-learned)

## User Flag

First, we run an Nmap scan on our target IP to identify which TCP ports are open.

`sudo nmap -sC -sV -p- -T4 --min-rate 1000 -oN nmap_full.txt 10.129.XXX.XXX`

The results from the scan:

<img width="400" alt="image" src="https://github.com/kittykrat/HacktheBox/blob/main/Boxes/Silentium/Images/slscan.png" />

```
RESULTS
```

***

### Results (Relevant Ports):

| PORT       | STATE           | SERVICE  | VERSION  |
|:-------------:|:-------------:|:-------------:|:-------------:|
| 22/tcp | open | ssh | OpenSSH 9.6p1 Ubuntu 3ubuntu13.15 |
| 80/tcp | open | http | nginx 1.24.0 |

- We then add the target IP to /etc/hosts so that we may access it by hostname:

`echo "10.129.xxx.xxx silentium.htb" | sudo tee -a /etc/hosts`

***

- We look for virtual hosts on our domain using FFuF

```
ffuf -w /usr/share/seclists/Discovery/Web-Content/common.txt \
     -u http://10.129.XXX.XXX/ \
     -H "Host: FUZZ.silentium.htb" \
     -ac
```

**Results**:

```
staging                 [Status: 200, Size: 3142, Words: 789, Lines: 70, Duration: 115ms]
```

***

- We look for subdomains on our domain using FFuF

```
ffuf -w /usr/share/seclists/Discovery/Web-Content/common.txt \
     -u http://silentium.htb/FUZZ \
     -ac
```

```
assets                  [Status: 301, Size: 178, Words: 6, Lines: 8, Duration: 112ms]
```

- We look for subdomains on the virtual host we found using FFuF

```
ffuf -w /usr/share/seclists/Discovery/Web-Content/common.txt \
     -u http://staging.silentium.htb/FUZZ \
     -ac
```

```
assets                  [Status: 301, Size: 156, Words: 6, Lines: 11, Duration: 108ms]
favicon.ico             [Status: 200, Size: 15406, Words: 99, Lines: 206, Duration: 107ms]
```


***

### Service Enumeration


***

## Root Flag


***

## Key Takeaways & Lessons Learned


