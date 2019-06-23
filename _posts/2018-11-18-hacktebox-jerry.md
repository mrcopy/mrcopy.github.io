---
layout: post
title: HackTheBox - Jerry
---

Jerry is one of the easiest boxes in [HTB](https://hackthebox.eu).

Access is obtained thanks to an Apache Tomcat service with default credentials (admin:admin) on port 8080. From there, it's pretty straightforward to get a shell: msfvenom to generate a `.war` file containing a remote shell connection back, uploading it to server from dashboard, accessing it from the browser and setting up an `nc` listener to receive the connection. Finally, in the Administrator's desktop, a file contains the two flags that were the initial goal. 

Let's continue with a more detailed explanation.

## Attack Narrative

### Port Scan

As always, I start with an initial scan of top 20 ports using `nmap`. This allows me to analyze quick results, and start with manual enumeration, while waiting for a full TCP and UDP scan. 

![Top 20 scan]({{site.baseurl}}/assets/htb-jerry/1.png)



Only one port seems to be open:

- Port 8080, http, running Apache Tomcat /Coyote JSP engine 1.1. Apache Tomcat/7.0.88



Meanwhile, a full scan of all 65535 ports gives me the same result:

![Full TCP scan]({{site.baseurl}}/assets/htb-jerry/2.png)



### Vulnerability Analysis

First thing I do is open in the browser port 8080 and check what's there:

![Port 8080 in Browser]({{site.baseurl}}/assets/htb-jerry/3.png)

The next step I always do when I find a service that requires authentication, is to check for common or default credentials. This time, I got lucky: I can log in using `admin:admin` as username and password. It's not always *that* easy, but more times that we can imagine, developers leave default credentials and don't bother to change them. It's always worth to check this first.

![Dashboard]({{site.baseurl}}/assets/htb-jerry/4.png)



### Exploitation

Once logged in and knowing the OS name (Windows Server 2012 R2), I can create a `.war` file containing a reverse shell. I use `msfvenom` for this:

![msfvenom]({{site.baseurl}}/assets/htb-jerry/5.png)



This file needs to be uploaded from the dashboard, so that's the next step. I go to the Tomcat Manager, find the section *WAR file to deploy* and upload it.

![War file to deploy]({{site.baseurl}}/assets/htb-jerry/6.png)



And then, I click Deploy. Once done, I can see it listed in the applications installed:

![List of deployed applications]({{site.baseurl}}/assets/htb-jerry/7.png)



If I click on `/shell` link, it'll redirect me to the page where it deployed my .war file. Once it does this, it will execute a reverse shell to port 4444 and will try to connect to my machine. I set up an `nc` listener for this, on port 4444.

![Nc listener]({{site.baseurl}}/assets/htb-jerry/8.png)

And then click on `/shell` to execute the .war code. 

![I've got a shell]({{site.baseurl}}/assets/htb-jerry/9.png)

I\'ve got a shell! 

I can now execute commands on the remote machine. And in this case, the service is running as Administrator, so I can simply navigate to the Administrator's Desktop folder and find my two flags:

![1542554766591]({{site.baseurl}}/assets/htb-jerry/1542554766591.png)



## Conclusion

Jerry was a fun box, and ideal for beginners. It involves a simple but effective attack that can be found in real assessments like a service running with default credentials *and* as Administrator. The use of `msfvenom` is also a good introduction to the tool.

If you get this far, thanks for reading and keep hacking!