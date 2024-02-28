# Agent Sudo

---

Machine by [DesKel](https://tryhackme.com/p/DesKel)

Tryhackme [link](https://tryhackme.com/room/agentsudoctf)

---

Tools Used:

- Kali Linux
- NMAP
- CURL
- hydra
- filezilla (or any other ftp client)
- binwalk
- zip2john
- John the Ripper
- stegcracker
- steghide

---

### 1. nmap scan

Enumerate the machine and get all the important information.

By using the next command:

`nmap -A VICTIM_IP`

We can see 3 open ports

![](img/AgentSudo1.png)

Visiting the website we can see the next message:

![](img/AgentSudo2.png)

### 2. Changing user agent

Due to the machine name and the end of the message I tried to change the user agent to use the alphabet with curl:

`curl -U "R" -L VICTIM_IP`

With the user agent as "R" this is what we see, knowing that there are 25 agents, plus the boss, wich is "R"

![](img/AgentSudo3.png)

I then tried to change the user agent to, "A", then "B" and finally "C" and found this:

![](img/AgentSudo4.png)

### 3. Hydra ftp brute force

Knowing that the user is "chris" with a weak password I then tried to use hydra with the john.lst dicctionary:

![](img/AgentSudo5.png)

And found that the password is "crystal":

![](img/AgentSudo6.png)

Then logged into the ftp server using "chris" and "crystal":

![](img/AgentSudo7.png)

Then downloaded all the files

### 4. Steganography

If we cat the content of file "To_agentJ.txt" we see:

![](img/AgentSudo8.png)

With this message we can deduce that one or more of the pics downloaded contains more info, probably with steganography, for that binwalk can be useful.

With binwalk on the two files we can see that "cute-alien.jpg" contains only a JPEG image and "cutie.png" contains, a PNG image, and ZIP files:

![](img/AgentSudo9.png)

- We want to know what's inside the zip file on the "cutie.png" photo, for that the first thing we'll do is extract the contents, we can see a new folder "_cutie.png.extracted":

    ![](img/AgentSudo10.png)

    The folder contains a txt file (it's empty) and a zip file, as it's password locked, we'll use the tool "zip2john" to extract a hash and then get it into a file we'll call "8702.hash", then using john the ripper we can get the original password, wich is "alien":

    ![](img/AgentSudo11.png)

- To get the steg password we will use the tool "stegcracker" on the image "cute-alien.jpg".

    ![](img/AgentSudo12.png)

Until we finally get the password "Area51":

![](img/AgentSudo13.png)

Then we will extract the zip file with the obtained password, now the file "To_agentR.txt" is completed and can be read:

![](img/AgentSudo14.png)

Then, with the "Area51" password we can use the tool "steghide" to extract the contents of the file "cute-alien.jpg" (we can only do this on .jpg files, as .png files are unsupported)

![](img/AgentSudo15.png)

### 5. SSH login

As we can see there's a username mentioned "james" and it's password "hackerrules!"

We can now try to login with ssh through this user:

![](img/AgentSudo16.png)

And we get the user flag on the user's home:

![](img/AgentSudo17.png)

### 6. Autopsy image reverse search

Then we can get the "Alien_autopsy.jpg" to investigate further, for that i'll use scp:

![](img/AgentSudo18.png)

Binwalk doesn't show anything out of the ordinary, there is some exif data, but it isn't useful.

A google images reverse search doesn't show anything, with tineye and the clue of fox news, i've found a website and the case:

![](img/AgentSudo19.png)

![](img/AgentSudo20.png)

### 7. Privilege escalation

I've tried to look for the linux kernel exploits, but couldn't find anything useful, so, with "sudo -l" we can find what privileges we have, we habe access to "/bin/bash", and with a google search of "exploit /bin/bash" we find this:

![](img/AgentSudo21.png)

Then put the code into a .py file and execute it, then input the current user and password:

![](img/AgentSudo22.png)

### 8. Root flag

Then we can cd into our root's home, cat the root.txt and get the root flag, and the Agent R name "DesKel".

![](img/AgentSudo23.png)
