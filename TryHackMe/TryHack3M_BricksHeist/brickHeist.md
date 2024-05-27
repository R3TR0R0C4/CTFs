# TryHack3M: Bricks Heist<!-- omit in toc -->

---

Machine by [tryhackme](https://tryhackme.com/p/tryhackme)

Tryhackme [link](https://tryhackme.com/r/room/tryhack3mbricksheist)

---

## Table of Content<!-- omit in toc -->

- [1. NMAP scan](#1-nmap-scan)
- [2. dirb](#2-dirb)
- [3. wpscan](#3-wpscan)
- [4. Exploit preparation](#4-exploit-preparation)


---

## Tools Used:<!-- omit in toc -->

- Kali Linux
- NMAP


---

### 1. NMAP scan

Using `nmap -sV -p-5000 IP` we will enumerate all the first 5000 active ports and grab all the service banners.

![](./img/01.png)

Visiting the http website we can see that there is an error 405, wich means the url we are trying to access is blocked.

![](./img/02.png)

Since there is a 443 port listening i've tried to visit the https site, this is what we can see, nothing special on the source code and not telling us much:

![](./img/03.png)


### 2. dirb

Since we can access the website with https, using dirb tells us there is a wordpress installation because of the `wp-admin` url:

![](./img/04.png)


### 3. wpscan

Using wpscan we can see that there are three vulnerabilities, one of them is an cross-site scripting, and two Remote code execution:

![](./img/05.png)
![](./img/06.png)

### 4. Exploit preparation

After looking for the RCE vulnerability i've found this [github](https://github.com/Chocapikk/CVE-2024-25600), we'll clone it:



Install the requirements with pip:

![](./img/07.png)

<br>

And execute with `python3 exploit.py --url https://bricks.thm/`

![](./img/08.png)

<br>

Once inside we can see we're inside the `/data/www/default` directory, inside there's the first flag `THM{fl46_650c844110baced87e1606453b93f22a}`

![](./img/09.png)

<br>

I've tried to get the root db password by listing the contents of `wp-config.php`, but the password field is protected by the `lamp.sh` script

![](./img/10.png)


https://github.com/aels/CVE-2022-2586-LPE