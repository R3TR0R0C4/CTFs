# Agent Sudo

---

Machine by [DesKel](https://tryhackme.com/p/DesKel)

Tryhackme [link](https://tryhackme.com/room/agentsudoctf)

---

1. nmap scan

    Enumerate the machine and get all the important information

    `nmap -A VICTIM_IP`

    ![](img/1.png)

    We can see 3 open ports.

    If we visit the web page of the machine we can see the next message:
   
    ![](img/2.png)
   
    Due to the machine name and the en of the message I tried to change the user agent to use the alphabet with curl:

    `curl -U "R" -L VICTIM_IP`

     With the user agent as "R" this is what we see, knowing that there are 25 agents, plus the boss, wich is "R"

     ![](img/3.png)
   
     I then tried to change the user agent to, "A", then "B" and finally "C" and found this:
   
     ![](img/4.png)

     Knowing that the user is "chris" with a weak password I then tried to use hydra with the john.lst dicctionary:

   ![](img/5.png)

And found that the password is "crystal"
   
   ![](img/6.png)

And logged in with filezilla:

   ![](img/7.png)

   Then downloaded the three files
   
   and cat the file "To_agentJ.txt":
   
   ![](img/8.png)

   we know that one of the files contains more data, with steganography, for that the tool binwalk will show

  By the contents of this file we can guess that one of the files has files hiden
   
     * There are 3 ports open.
  
     * You redirect yourself to a secret page changing the user-agent
   
     * The agents name is "chris"

3. 
