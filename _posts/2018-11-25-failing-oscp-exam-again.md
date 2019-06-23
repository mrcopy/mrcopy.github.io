---
layout: post
title: Failing the OSCP Exam... Again
---

I want to talk about my experience of failing the OSCP exam twice.

I want to explain in detail how I prepared after failing the first time, how I felt the week before the exam, how the exam went (what went right and what went wrong), how I felt afterwards and what am I going to do now.

### How I Prepared

After failing for the first time, I made a list of things to improve for my next try. There were two things I really wanted to polish before my next attempt, and those were:

- Web Application Pentesting
- Privilege Escalation Techniques

After I knew that I didn't pass on my first try, I immediately scheduled another date for the exam, and started studying, working on the weak spots. My lab time ended months ago, so I practiced using Hack The Box.

Now, I **love** Hack The Box: the community is great and very friendly towards beginners, there's a *huge* number of machines (89 at the time of this writing) to practice (without counting challenges) if you pay VIP servers and after my lab ended, I felt like home there. The only problem with it that I found to use it to prep for OSCP,  is that most machines tend to be very CTF-like. Not all of them, of course, but a good number of them it is. This is *unrealistic*. Does it help you become a better pentester? Absolutely, but it's still unrealistic, and the best thing about OSCP labs is that it really looks like a real network, with users, misconfigured systems and so on.

But I kept trying, improving my craft. I popped a few more boxes throughout the month between my first exam and my second one, and it really helped me get a good understanding of more concepts in detail. 

Throughout that month:

- I learned a much more robust process for testing Web Apps.
- I practiced as much as I could Privilege Escalation techniques.



### The Week Before

I studied for long hours and for many days. I was feeling confident, I was improving and I was really obsessed with passing.

However, something happened the week previous to the exam. I can't explain what it was or at what particular moment, but as the exam day was approaching, I felt more and more tired of everything. The last three/four days, I really didn't wanted to turn on the computer. 

I have been studying so hard and for such long hours that I didn't realize that the obsession to pass had started to affect me, both mentally and physically. That's not right. I wasn't talking with my family too much, I wasn't practically going out, I was just focused on learning, obsessed with knowing more and more that at some point, just the week before, I had enough. 

It was too late to rest. I mean, I did rest completely the day before, but it wasn't enough. I needed *more* rest, but I didn't had time. Mentally, I felt exhausted. 



### The Second Try

This is the chronicle of how it all went:

- It all started smoothly. After the initial screening process and once I got the VPN connection package, I forced myself to really dive in the boxes, even that I was physically and mentally tired. In the first hour, I rooted the easy box. 
- I spent 2-3 hours in another box. This one was hard, couldn't figure it out a way in. I took a break a couple of minutes, and decided to rotate machines, which was my initial plan. 
- I spent another 2 hours in this third box. I knew how to get it, but the code was not giving me a shell back. Odd. I took another break, and decided to rotate machines again.
- I moved to the fourth machine. This one was easy, and in 30 minutes I pwned it. I had 35 points, close to 7 hours into the exam. I took a longer break, probably around 45 minutes and had dinner. 
- After my break, I decided to tackle my fifth machine, and after an hour or so... Nothing. I kind of knew how to access it, but I just wasn't able to connect all the dots. I needed some sleep, so I called the day. Before going to sleep, I spent a few minutes considering my options for each machine, kind of deciding where to focus once I get up the next day, and so on.
- I slept around 6 hours, maybe a bit more. In my first try, I just slept 30 minutes, and it really made a difference in the final hours (I really feel that I could've passed on my first try). I made breakfast, and started working on the third box, the one that I felt that I was very close to get access.
- I can't remember exactly around what time, but at some point in the morning, I decided to quit doing what I was doing (modifying/writing an exploit that didn't work!) and use Metasploit. Boom, in 5 minutes, I was in. "10 more points", I thought. I was now at 45 points, and although it was a confidence boost, soon I lost it again. I just couldn't escalate to root/Admin on that box, and after some time trying, I decided to shift machines again.
- I tried for 23:45 minutes to pass the exam, I didn't stop until my VPN connection dropped. I was tired, I was mad with myself for not being able to realize the previous days that I needed a break, and I knew I didn't have enough points to pass. OSCP had done it again: crushed me like the first time, this time more painful.

I didn't send my report afterwards, which I now recognize that it was a mistake. I was mad, tired, and didn't bother knowing that I was not going to pass anyway. 



#### What I Get From It All

Some things that went right:

- *Time management was better*. I slept around 6 hours, rotated machines often, took frequent brakes. The first exam, [I really failed at this](https://mrcopy.gitlab.io/failing-oscp-exam.html).
- *Understanding Buffer Overflows*, although I initially skipped them in the material (I didn't get them, and I was scared of them) I now feel absolutely confident that I understood the simple concepts behind overflowing the stack, or at least simple exploitation. I know that there are still a lot of things to learn about, but the knowledge I've acquired is more than enough to pass OSCP.
- *It was a humbling experience*. I really like the security industry. I want to work and develop a career in it, so this experience taught me that I'm in a moment of my education that I know that I don't know a lot things. It's humbling. It makes you want to keep working, keep studying, keep trying and doing it harder. 

Knowing that I failed really crushed me. I've been wanting to do this certification for many years, and I approached this second try really confident that I will pass it. I'm not crushed, or mad anymore, because I understand that in the end, that's what OSCP is: a *humbling experience*. A **good** one.

### What Now?

I am not done yet.

I will continue to pursue my dream of becoming an OSCP, but I will take a break from it till next year. I want to do it right, take the course again, buy 60 more days and pwn *all* boxes this time.

If you are reading this and you are thinking of enrolling in the course, I really hope that this post encourages you to do it. OSCP is unique and will teach you many things about Pentesting, but also many other things about yourself. If that's the case, then good luck and thanks for reading!