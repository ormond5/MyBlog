---
layout: post
title: TryHackMe-Lord-Of-The-Root
subtitle: Walkthrough
tags: [sqlmap, dirbuster, privilege escalation]
image: pictures/Lord/lord.jpeg
comments: true
---

You can access this room here: [LOTR](https://tryhackme.com/room/lordoftheroot)


__This room requires a HackTheBox subscription to join__

This room is apart of the TryHackMe’s OSCP path and it was aimed to be a similar difficulty to machines on the OSCP exam.


## Table of Contents

- [Task 1: Nmap ](#nmap)
- [Task 2: Web Recon](#Web-Recon)
- [Task 3: SQLMAP ](#SQLMAP )
- [Task 4: Privilege Escalation](#Privilege-Escalation)



**Let's Begin!**

### Nmap

Deploy the box and run a nmap scan against the IP given to you!

```nmap -sC -sV -vv -p- <boxIP> -oN nmap-scan.txt```

The nmap scan will return back two open ports, port 22 and port 1337

 ![nmap_scan](/pictures/Lord/http.png)

### Finding Exploits

### Finding Flags

#### Video

I made a video for those visual learners! Enjoy! 



