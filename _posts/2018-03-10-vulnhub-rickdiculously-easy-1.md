---
layout: post
title: Vulnhub - Rickdiculously Easy 1
---

#### The Target

I loved this CTF (probably I loved it more because I am a big fan of Rick and Morty).

Fun as hell and I learned a lot. I initially had some problems with the enumeration, I wasn't doing it correctly. Some ports where not showing, and when I got stuck, I had to do some research and do a more complete port scan: great lesson learned.

**Goal:** Get the FLAG.txt files totalling 130 points.

#### The Process

I'll start with an arp-scan to detect the target on the network.

![Arp-scan]({{site.baseurl}}/assets/rickdiculously-easy-1/1.png)

Then, quick nmap SYN scan to see which ports it has open.

![Nmap SYN scan]({{site.baseurl}}/assets/rickdiculously-easy-1/2.png)

The following ports are open:

- 21/tcp ftp
- 22/tcp ssh
- 80/tcp http
- 9090/tcp zeus-admin

It seems that port 80 is open. Let's check it on the browser:

![Check port 80]({{site.baseurl}}/assets/rickdiculously-easy-1/3.png)

A big face of a Morty appears (dimension C-137?). However, not the website or the source code tells us anything.

![Big Morty Face]({{site.baseurl}}/assets/rickdiculously-easy-1/4.png)

I'll run a command that searches not only for open ports, but also for vulnerabilities. The nmap -sC scan runs a script scan, and in this case with default scripts. In a nutshell, nmap also has a scripting engine besides the normal commands for port discovery. You can specify what scripts you wish to run o run the default scripts (to see the scripts, check **nmap --script-help** default).

This is the result of the script scan:

![SC Nmap Scan]({{site.baseurl}}/assets/rickdiculously-easy-1/5.png)

Pretty interesting things here. Above all things, that it allows anonymous FTP login, and even lists the files in the root directory (first FLAG.txt!) because of ftp-anon.

I'll connect anonymously to ftp. I type 'ftp HOST' and as username and password: "anonymous".

![FTP connection]({{site.baseurl}}/assets/rickdiculously-easy-1/6.png)

I'm in. Now, I'll download the FLAG.txt file and check it locally.

![Check first flag]({{site.baseurl}}/assets/rickdiculously-easy-1/7.png)

'FLAG{Whoa this is unexpected}'. Great, our 10 first points.

After looking for clues in the ftp port, I decide to move on and run a nikto scan on port 80, to see if there's anything there. Here are my findings:

![Nikto scan port 80]({{site.baseurl}}/assets/rickdiculously-easy-1/8.png)

Right. A '/passwords' directory in the wid. I'll access host/passwords in the browser:

![Explore /passwords directory]({{site.baseurl}}/assets/rickdiculously-easy-1/9.png)

Obviously, I open the file FLAG.txt and see this:

![Second FLAG.txt]({{site.baseurl}}/assets/rickdiculously-easy-1/10.png)

'FLAG{Yeah d- just don't do it}'. Another flag, 20 points.

I'll go to /passwords.html now:

![Check passwords.html file]({{site.baseurl}}/assets/rickdiculously-easy-1/11.png)

Rick was here! Let's check the source code:

![Source code of passwords.html]({{site.baseurl}}/assets/rickdiculously-easy-1/12.png)

Ok, a juicy piece of information. Password: winter. Maybe we can log in with ssh? 

![Password:winter]({{site.baseurl}}/assets/rickdiculously-easy-1/13.png)

Nope, it's refusing the connection. I don't know if the users are correct, though. Pure guessing... Well, let's keep going. The directory /icons is not showing anything interesting, either.

Let's access port 9090, that is running Zeus Web Server.

![Access Zeus]({{site.baseurl}}/assets/rickdiculously-easy-1/14.png)

Another flag: 'FLAG{THERE IS NO ZEUS, IN YOUR FACE!}'. Total: 30 points so far.

I'll use dirb to brute-force and find hidden web objects in port 80 to see if there's any other important thing that we missed.

![Dirb brute force]({{site.baseurl}}/assets/rickdiculously-easy-1/15.png)

There is an interesting /robots.txt file. I'll check it in the browser. 

![Robots.txt file]({{site.baseurl}}/assets/rickdiculously-easy-1/16.png)

Whoa, Rick is everywhere! there is a /cgi-bin/root_shell.cgi resource. If we check it it says UNDER CONSTRUCTION, but if we check the source... There it is Rick again!

![Check .cgi]({{site.baseurl}}/assets/rickdiculously-easy-1/17.png)

Dead end. I don't think I would get anything better in root_shell.cgi.

At this point, I decided to enumerate a bit more. I felt like I missed some attack vectors, so I tried doing a scan 'nmap -Pn -p- -sV 10.0.2.10' and surprinsingly it shows more ports open: 

![Enumerate a bit more, top ports using nmap]({{site.baseurl}}/assets/rickdiculously-easy-1/18.png)

They are running unknown services. I guess I can simply connect using netcat:

![Connecting using netcat]({{site.baseurl}}/assets/rickdiculously-easy-1/19.png)

A flag.

![Another flag using netcat]({{site.baseurl}}/assets/rickdiculously-easy-1/20.png)

Another one.

Whoa. I didn't enumerate correctly at first. There were two beautiful hanging fruits that I didn't see at first, and that I should've saw. Great lesson: always enumerate properly before jumping into exploiting vulnerabilities. 

Ok. So far, I already have **50 points**. This is the list of found flags:

- FLAG{Whoa this is unexpected} - 10 points
- FLAG{Yeah d- just don't do it.} - 10 points
- FLAG{THERE IS NO ZEUS, IN YOUR FACE} - 10 points
- FLAG:{TheyFoundMyBackDoorMorty} - 10 points
- FLAG{Flip the pickle Morty!} - 10 points 

First I check if users such as 'morty' or 'rick' use the password 'winter' on port 22222 using ssh. No luck, but it's pure guessing. If I can get a list of users though...

Let's go back to our cgi-bin directory. I've already opened this one, but now let's go to traceroute.cgi:

![Traceroute.cgi]({{site.baseurl}}/assets/rickdiculously-easy-1/21.png)

It seems to be some sort of website to do a traceroute. The source code of the page doesn't contain anything interesting, so let's try the program.

![Try traceroute.cgi program]({{site.baseurl}}/assets/rickdiculously-easy-1/22.png)

Ok, it seems to run traceroute (duh!) and show the result of the command below the text area. Great... Something tells me that this is running the commands directly into the server without any sanitization or anything. Let's try to inject a command then, and see if it works. I'll type 'localhost; ls' and see what I have:

![Command injection]({{site.baseurl}}/assets/rickdiculously-easy-1/23.png)

It works! It's running the commands directly, without sanitization! Remember that I needed some users because I have the password 'winter'? Maybe I can get the list of users this way.

First, who am I?

![Who am I?]({{site.baseurl}}/assets/rickdiculously-easy-1/24.png)

A user called apache. Can I get the list of users this way?

![List of users]({{site.baseurl}}/assets/rickdiculously-easy-1/25.png)

Wait what??! A fricking cat! This is no list of users! Let's use a different command, like 'tail':

![Tail command]({{site.baseurl}}/assets/rickdiculously-easy-1/26.png)

Voila! Three users: Morty, RickSanchez and Summer. I bet the password we have belongs to one of these.

I'll connect to the port 22222 using ssh and try with the three:

![Connect to ssh]({{site.baseurl}}/assets/rickdiculously-easy-1/27.png)

No luck with 'Morty' or 'RickSanchez', but with 'Summer':

![No luck trying]({{site.baseurl}}/assets/rickdiculously-easy-1/28.png)

I'm in. Time to explore the system from the inside!

![I'm in]({{site.baseurl}}/assets/rickdiculously-easy-1/29.png)

We get a... cat?! and a FLAG.txt. It seems that everytime I write the cat command, a wild cat appears!

'FLAG{Get off the high road Summer!} - 10 points'. Total: 60 points.

If I do a 'locate FLAG.txt' I get this result:

![Locate flag]({{site.baseurl}}/assets/rickdiculously-easy-1/30.png)

I already have those, nothing new.

After poking around a bit, I noticed that Summer has access to the directories of the two other users: Morty and RickSanchez.

![Access other directories]({{site.baseurl}}/assets/rickdiculously-easy-1/31.png)

There are two interesting files in the Morty directory, so I decided to copy those two to my local pc to analyze: one's a .zip and the other one is a .jpg image.

![Analyze files]({{site.baseurl}}/assets/rickdiculously-easy-1/32.png)

Now I've got the two files to analyze.

![Analyze files 2]({{site.baseurl}}/assets/rickdiculously-easy-1/33.png)

Opening the image doesn't show anything relevant, it's just a Rick talking. What I can do is analyze its metadata with the strings command, that basically extracts text from binary files. Along with gibberish, I find the message:

![Find message in image]({{site.baseurl}}/assets/rickdiculously-easy-1/34.png)

Nice, the password of the .zip file. I'll unzip it and...

![Unzip file]({{site.baseurl}}/assets/rickdiculously-easy-1/35.png)

'FLAG: {131333} - 20 Points'. One more! Now I'm at 80 points so far. 50 to go, though...

I know what safe Morty is talking about: the one that's on RickSanchez's home directory. Let's get it:

![Obtain file from Rick]({{site.baseurl}}/assets/rickdiculously-easy-1/36.png)

I don't have permissions to run it, but I can copy it to Summer's home dir and execute it there. After doing it, it seems that I need to use some arguments to run the file. Luckily, Morty provide us with it in the last flag! 131333 is the argument:

![Copy file and execute it]({{site.baseurl}}/assets/rickdiculously-easy-1/37.png)

Another flag! 'FLAG{And Awwwaaaaayyyy we Go!} - 20 Points'. I have now 100 points!

I quickly google for Rick's band name: "The Flesh Curtain". Here's what I did: I used crunch to create two lists of passwords: list-flesh.txt and list-curtains.txt.

![Create list 1]({{site.baseurl}}/assets/rickdiculously-easy-1/38.png)
![Create list 2]({{site.baseurl}}/assets/rickdiculously-easy-1/39.png)

The command I used specifies: the min-max length of the word (7 and 10), the output file (option -o) and a pattern to respect, in this case , for an uppercase letter and % for a digit, followed by the word "Flesh" and "Curtains".

After that, I ran hydra to brute-force ssh login on port 22222 using the username 'RickSanchez'. I tried with the two lists, and after a while I got this results, with the list-curtains.txt file: 

![Obtain password]({{site.baseurl}}/assets/rickdiculously-easy-1/40.png)

Gorgeous. Password: **P7Curtains**. I connect now through ssh and see this:

![Ssh login]({{site.baseurl}}/assets/rickdiculously-easy-1/41.png)

I'm in, as a user with surely sudo privileges. Let's check this:

![Sudo privileges]({{site.baseurl}}/assets/rickdiculously-easy-1/42.png)

I quickly list the files, will actually look the same, but I find in the /root directory another sweet flag:

![Sweet flag]({{site.baseurl}}/assets/rickdiculously-easy-1/43.png)

'FLAG: {Ionic Defibrilator} - 30 points'. Those are the last points we were looking for!

**Total: 130 points.**

Challenge completed!

