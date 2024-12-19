# ShowTime<!-- omit in toc -->

Machine by maciiii___

---


## Table of Content<!-- omit in toc -->

- [1. nmap scan](#1-nmap-scan)
- [2. SQL injection](#2-sql-injection)
- [3. Using the stolen credentials](#3-using-the-stolen-credentials)


---

### 1. nmap scan

Enumerate the machine and get all the important information.

By using the next command:

`nmap -sV -p- VICTIM_IP`

We can see 2 open ports with no version vulnerability

![](./img/01.png)

After visiting the website we can't see any aparent vulnerability, but there is a button to the login screen:

![](./img/02.png)

This is the login screen:

![](./img/03.png)

### 2. SQL injection

I'll execute sqlmap with the option `-u http://172.17.0.2/login_page/index.php` to indicate the target, and `--forms` to see the available html formularis.

![](./img/04.png)

After the analizing the site we can see that the login page is vulnerable to some attacks like boolean-based blind, error-based and time-based blind.

After this we can successfully try to use the option `--dump`:

![](./img/05.png)

After doing the same steps as before, we can execute the vulnerabilities and see three users with the passwords:

![](./img/06.png)

### 3. Using the stolen credentials

Let's try one of the credentials we've just gotten:

![](./img/07.png)

And successfully login:

![](./img/08.png)