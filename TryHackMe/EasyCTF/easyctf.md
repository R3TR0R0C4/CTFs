# Simple CTF<!-- omit in toc -->

---

Machine by [MrSeth6797](https://tryhackme.com/p/MrSeth6797)

Tryhackme [link](https://tryhackme.com/r/room/easyctf)

---

## Table of Content<!-- omit in toc -->

- [1. Nmap Scan](#1-nmap-scan)
- [2. Dirb Scan](#2-dirb-scan)
- [3. CMS Made Simple Exploitation](#3-cms-made-simple-exploitation)
- [4. SSH login](#4-ssh-login)
- [5. User flag](#5-user-flag)
- [6. Privilege escalation](#6-privilege-escalation)
- [7. Root flag](#7-root-flag)



---

## Tools Used:<!-- omit in toc -->

- Kali Linux
- NMAP
- Dirb


---

### 1. Nmap Scan

Doing a nmap scan reveals a webserver, ftp server and ssh on port 2222:

![](img/01.png)

<br>

### 2. Dirb Scan

We can see a index, wich turns into a default apache installation page, a robots.txt with nothing interesting in it, and a "cms made simple" installation:

![](img/02.png)

<br>

### 3. CMS Made Simple Exploitation

As we can see at the bottom of the page, the version of the Simple CMS is 2.2.8:

![](img/03.png)

<br>

After googling `Simple CMS 2.2.8`, we can see this exploit on [exploit-db](https://www.exploit-db.com/exploits/46635):

![](img/04.png)

<br>


The exploit consists of a python script that we'll execute with the options `--url=http://Server-IP/simple/` and `--wordlist=/usr/share/wordlists/rockyou.txt`:

![](img/05.png)

<br>

After executing it, this  is the result:

![](img/06.png)

As we can see the provided username and password seems to not be correct:

![](img/07.png)

<br>

### 4. SSH login

I've tried to use hydra to get the ssh password with the previous username, as we can see we get a password, `secret`:

![](img/08.png)

<br>

Let's try to login with ssh:

![](img/09.png)

### 5. User flag

In the mitch home folder is the first flag:

![](img/10.png)

### 6. Privilege escalation

With `sudo -l` we'll check if there is any file or executable to wich we have root access without a password, as we can see we've got access to vim as root:

![](img/11.png)

<br>

Let's execute vim with sudo:

![](img/12.png)

<br>

And execute bash:

![](img/13.png)

<br>

As we can see we're root:

![](img/14.png)

<br>

### 7. Root flag

Let's get the root's flag:

![](img/15.png)
