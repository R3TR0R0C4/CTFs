# Bounty Hacker<!-- omit in toc -->

---

Machine by [Sevuhl](https://tryhackme.com/p/Sevuhl)

Tryhackme [link](https://tryhackme.com/room/cowboyhacker)

---

## Table of Content<!-- omit in toc -->

- [1. Nmap Scan](#1-nmap-scan)
- [2. anonymous ftp](#2-anonymous-ftp)
- [3. locks and task](#3-locks-and-task)
- [4. Hydra](#4-hydra)
- [5. SSH login and first flag](#5-ssh-login-and-first-flag)
- [6. root flag](#6-root-flag)

---

Tools Used:

- Kali Linux
- NMAP
- ftp client
- hydra
- ssh client

---

### 1. Nmap Scan

Doing a full nmap was taking too long, so i'll limit it to the first 10.000 ports, this is the result

![](img/bountyhacker01.png)

Visiting the website we can't really see anything useful.

<br>

### 2. anonymous ftp
   
Trying to login as anonymous is successful, but trying to list the directory we get a message:

`229 Entering Extended Passive Mode (|||9521|)`

![](img/bountyhacker02.png)

After a while we get a connection time out error, and then the result of the dir command:

![](img/bountyhacker03.png)

We can see 2 files `locks.txt` and `task.txt`, we'll download them and see what's inside.

<br>

### 3. locks and task

The file `locks.txt` contains what seems to be some passwords.

![](img/bountyhacker04.png)
        
And the file `task.txt` a couple of tasks and a name:

![](img/bountyhacker05.png)

<br>

### 4. Hydra
   
After getting what looks like some passwords, i'll be using hydra to try and breake my way in through SSH.

For that i'll be needing a dicctionary with usernames, since i don't know wich one it is, to make it i'll be using all the names i've found on the website, both starting with uppercase and lowercase, and the username `lin` that i've found on the `task.txt` file:

This will be the resulting dictionary:

![](img/bountyhacker06.png)

And this the hydra command:

![](img/bountyhacker07.png)

As we can see the password for `lin` is `RedDr4gonSynd1cat3`.

<br>

### 5. SSH login and first flag

After getting the password for the user lin, we can see on the Desktop the file `user.txt`, that's the first flag:

![](img/bountyhacker08.png)

<br>

### 6. root flag

After looking around the filesystem and not finding anythin i tried to use my sudo permissions on the root's home folder, but turns out I don't have the permissions to do that:

![](img/bountyhacker09.png)

Since i can't see what's inside the root folder, and probably contain the root's flag, i used `sudo -l` to see what i have permissions on, turns out that i can use the binary `tar`:


![](img/bountyhacker10.png)    

Then i can attempt to compress the contents of the root folder:

![](img/bountyhacker11.png)

And decompress it:

![](img/bountyhacker12.png)

We can see that we found the root flag:

![](img/bountyhacker13.png)