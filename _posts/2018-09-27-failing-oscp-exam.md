---
layout: post
title: Failing the OSCP Exam
---

Date: 2018-09-27
Category: OSCP
Tags: oscp, exam
Slug: failing-oscp-exam


Today I received an email from Offensive Security that I didn't meet the requirements to pass the OSCP exam in my first try. I think that it will be a good thing to write about the experience, determining what I did right and what wrong, and specially what I can change for the next try.

### Before the Exam

Two days before the exam, I spent the entire day practicing Buffer Overflows: it was a fantastic idea, and paid off in the exam at the end. 

The resources I use where:

- [Veteransec's Buffer Overflow Tutorial](https://veteransec.com/2018/09/10/32-bit-windows-buffer-overflows-made-easy/). Best resource for the actual process... I don't know why, but this post made me really enjoy the practice of exploit development.
- [dotstackbufferoverflowgood](https://github.com/justinsteven/dostackbufferoverflowgood), which is a fantastic tutorial and helped me a ton.
- Other programs found on [exploit-db](https://www.exploit-db.com).

If you are thinking on doing the exam, please make sure that you understand the process well, from fuzzing to shellcode. You should be able to do it in the exam in the first two hours, and it's 25 points! It's almost like you started the thing and already have those points.

The day before the exam, I spent it running like a maniac in a football field. Yes, once I made sure that I had the scripts in place, notes ready, a plan of attack, then I didn't wanted to think about it for a day, and then hit it with everything I had. 

Smart choice: make sure to rest or to disconnect completely the day before or at least a part of the day. For me it was running and playing footbal, but for you might be something different. And when I started the exam, I had my mind fresh and ready to hack!

### The Exam

I started the exam at 3pm. I did a proctored version of the exam, and honestly is not that bad at all. You have to scan the room once, show a document to the camera, run a script and paste the results, and that's pretty much it. Also, you have to remember to let the proctor know when you are leaving, or taking a break. 

The only thing that made me hate it a bit was when somehow at some point the screen connection dropped a few times... Like five times in an hour. At that time I thought "This is how it's going to be?!". But then, no problem whatsoever.

Timeline of the exam:

- 3 hours onto the exam, I had one root and another low privilege shell. 

- 8 hours onto the exam, I had two roots, and two low privilege shells. I was golden!

- I stopped at around 00:30 and went to sleep and rest for a few hours. I felt that I was in great shape, and that the next day I would be able to escalate privileges in the two.

- At around 3am, I woke up and continued with the exam: I just couldn't sleep more than an hour and a half! My mind was racing at top speed. It was a dumb move, I should've slept at least a couple of hours like I had planned.

- At 7am of the next day, I obtained root access to one of the machines. So, at that time I had three roots, and one low privilege shell. I felt confident that I could pass, but I felt really tired already.

- From 7am to 2pm, I threw absolutely everything I could think of to the other two machines. At the end, I was just a mess typing commands without thinking, trying exploits without any actual plan whatsoever. This is something I want to improve for the next try: have some hours of sleep, because the last hours of the exam might be crucial!

- The last 45 minutes I was destroyed, but I made sure to have all the necessary screenshots to write the report. 

Now, Offensive Security only tells you if you pass/fail. However, it would've been nice to know where exactly I was missing the privilege escalation. I just felt like I was so close: had a few credentials, another hashed password and username... But still couldn't connect the dots.

### After the Exam

Even that I knew that the possibilities of passing where absolutely slim, I felt extraordinarily good with myself. I think it was a very good first try, and I had the thought that I could've done a little better. Still, I took the time and wrote the report, with a total of 40 pages.

I didn't find the difficulty of the machines particularly hard, but lack of methodology and order from my side in the actual pentest was key. Also, time management: it's absolutely essential to manage the time accordingly in such a long exam.

Around 24 hours later of my submission, I received an email that I failed.

### Things to improve

Here's a list of key things to improve for the next exam:

- **Time management**. Really, stick to the plan: rest, rotate machines, take breaks, hidrate.
- **Privilege Escalation**. Make sure to practice many privilege escalation techniques with enough machines from Hack The Box and Vulnhub. It's important to know well the likely vectors that might give you the chance for escalation (permissions, kernel exploits, SUID programs, etc).
- **Web Application Pentesting**. Develop a clear methodology for attacking web apps. 
- **Modify scripts**. Some things didn't go as planned, so modify the scripts to enumerate in a more structured manner.

### What now?

Well... I'll keep trying, but this time harder! I'm going to focus on the list of things to improve above and then scheduling another try. 

This next time though, hopefully, I'll become an OSCP once and for all!