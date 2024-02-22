---
title: Teach Yourself Infosec
description: 
published: true
date: 2024-02-22T19:08:35.745Z
tags: 
editor: markdown
dateCreated: 2024-02-22T06:13:20.040Z
---

# Teach Yourself Infosec (20XX Edition)
> A modified resource dump: By [Ryan](https://an00brektn.github.io)
> Inspired by y5's [teachyourselfinfosec.com](https://teachyourselfinfosec.com)
{.is-info}

Disclaimer: **I still have a long way to go, and I do not know everything, and I never will know everything,** but if you want to get really good at things quickly, there are resources out there for you. This document will be **long**, but it's all good stuff.
Note that I am definitely biased with what resources I have, and I wish this list was a bit more balanced. If you have any resources you would like to see, please let me know, definitely some "TODOs" lying around in this PDF. You could also google for "awesome security resources list" on GitHub and probably find *even bigger* lists, but this is mostly resources that have worked for me or stuff I know works for others. 

## 5 Pillars of Infosec
> Stolen from [DFIRmadness](https://github.com/DFIRmadness/5pillars/blob/master/5-Pillars.md)
{.is-info}

### Motivation
Quote DFIRmadness:
> "Mastering the basics will make you exceptional." 
> Master the basics and then specialize to be an invaluable asset to your team.
   To start, or level up, a career in Information Security (aka Cyber) Security you need to be proficient in five key areas of technical skills. You don't have to _master_ each one of them, but you need to have a good understanding of them. Whether you want to go offense or defense you will need to be functional in these five areas: **General computing, Networking, Scripting/Programming, Linux/MacOS, Windows**.

To do security, you have to know what you're securing and how it works, simple as that. No one's expecting you to do a Computer Science degree overnight, but trying to gain a fundamental understanding of these five categories (basically completing most of your 200-300 level classes in your major) is pretty important. Again, no one's expecting you to know everything.

I encourage you to read through the original post, and I won't cover everything here, but here is an outline of resources/classes that are relevant.

### Resources
- **General Computing**
	- Don't worry too much about this one, college does a decent job at teaching these things
	- Classes
		- Intro to Computer Science
		- Computer Organization and/or Operating Systems
	- Resources
		- [Professor Messer A+](https://www.professormesser.com/free-a-plus-training/a-plus-videos/220-1001-training-course/) - Just basic computer knowledge
		- [Threads v Processes](https://www.youtube.com/watch?v=O3EyzlZxx3g) - Lecture explaining these things
		- [UMass Operating Systems Lectures](https://youtube.com/playlist?list=PLacuG5pysFbDQU8kKxbUh4K5c1iL5_k7k) - In case you want to get ahead of the game with that class
- **Networking**
	- Classes
		- Intro to Networks
		- Lewis offers two more classes in that series, "Switching, Routing, and Wireless" and "Network Security", both of which are very good if you want to up your networking game. Note that they go pretty deep into networking, which can be very different from competitions, other classes, etc.
	- Resources
		- [Professor Messer Network+](https://www.professormesser.com/network-plus/n10-007/n10-007-training-course/) - Teaches the basic theory of networking, not a lot of hands on
		- [HTB Academy: Intro to Networking](https://academy.hackthebox.com/module/details/34)
		- [Packet Tracer](https://www.netacad.com/courses/packet-tracer) - Free software that allows you to visualize what's going on in a Network and get "hands-on" with a simulation. Cisco also offers some free intro courses
- Programming
	- Classes
		- Bascially 25-50% of your degree if you're CS or Cybersecurity
	- Resources
		- [freeCodeCamp.org](https://www.youtube.com/channel/UC8butISFwT-Wl7EV0hUK0BQ) - Literally teaches the basics of any language your heart could desire
		- [Harvard CS50](https://cs50.harvard.edu/x/2021/) - You will know most of this stuff from Intro to CS, but the C stuff is what matters
		- There's no need to become the Rosetta stone of languages. Python and JavaScript seem to rule the world these days, and the rest depends on what you focus on. **Learn languages as you need them, don't learn it for the sake of saying "I know how to program in QBasic"**.
			- Cool languages that might be interesting to look at though if you're bored: C/C++, Rust, C#, Golang, JavaScript, Powershell, Ruby
		- [Code Wars](https://www.codewars.com/) - more leetcode style problems, but definitely small, neat challenges that can help get you some practice with a new language if you don't have time for a full project
- **Linux**
	- Classes
		- Intro to Unix (Cybersecurity majors are forced to take this. CS majors can choose to do it as an elective, or you could literally learn this one on your own, up to you)
	- Resources
		- [Over the Wire: Bandit](https://overthewire.org/wargames/bandit/) - An online game where you have to run specific linux commands to find the next part. Excellent resource to get familiar with the Linux command line.
		- [TryHackMe: Linux Fundamentals](https://tryhackme.com/room/linuxfundamentalspart1) - They just teach you the commands in an interactive way
		- [The Taggart Institute](https://taggartinstitute.org/) - A pay-what-you-can platform offering text-based courses on regular expressions, vim, the Linux command line, etc.
- **Windows**
	- Classes
		- Operating Systems
		- Computer Security 1
		- I actually have no clue if there's a class that just focuses on Windows
	- Resources
		- [HTB Academy: Windows Fundamentals](https://academy.hackthebox.com/module/details/49) and [HTB Academy: Intro to Active Directory](https://academy.hackthebox.com/module/details/74) - first one is more focused on working in command line, second is focused on Active Directory, one of the most common Identity Access Management systems out there 
		- [TryHackMe: Windows Fundamentals](https://tryhackme.com/room/windowsfundamentals1xbx) - Everything from the GUI to the Registry
		- [Microsoft Docs (MSDN)](https://docs.microsoft.com/en-us/) - Gotta love documentation
		- [Windows Internals](https://learn.microsoft.com/en-us/sysinternals/resources/windows-internals) - tons of good info here, but can be a little advanced if you're just starting out (maybe visit this later when you want to get spicier with things)

Once again, I encourage you to check out [DFIRMadness](https://github.com/DFIRmadness/5pillars/blob/master/5-Pillars.md) for the wonderful document they put together to highlight this. Now, time for the main show.

## The Dump
### Intro
Ok, so there are a LOT of different areas of infosec, so to keep things simple, I will provide resources under general headings. 

**Please note, you do not need to be an expert in all of these domains, and there is no need to feel obligated to stick yourself into a single hole. Generally, people will develop a specialization in 1-3 domains as they progress in their careers, but just do what makes you feel like you're progressing and you'll find your way.**

### Tooling
- Install [VirtualBox](https://www.virtualbox.org/wiki/Downloads) and the extension pack.
	- A lot of security work is done in Virtual Machines (VM's) so we make sure we isolate our test environments from our personal data
- Use VirtualBox to install [Kali Linux](https://www.kali.org/get-kali/#kali-virtual-machines), which comes prebuilt with a lot of hacking/security audit tools
	- With time, you will become more proficient with virtualization and distributions where you get to the point where you want things done a certain way, but for now, Kali is good enough.
	- Official Guide: [link](https://www.kali.org/docs/virtualization/import-premade-virtualbox/)
- Get yourself a code development environment like [Visual Studio Code](https://code.visualstudio.com/)
	- When you take classes with professors, each one will probably tell you to use a different environment (e.g. PyCharm, IDLE, Eclipse, Notepad++). It's more important that you know your tool well, instead of copying what a teacher does on a program and then never using it again. **TL;DR: Use whatever you want I'm not your mom**
- Install [Git](https://git-scm.com/downloads), you should learn it.
	- [Gamified Learning Git](https://learngitbranching.js.org/)

### Networking/Homelabbing
- Network Engineering and Homelabbing could be a whole document on their own, so I highly recommend finding someone who does this as a hobby and just asking them
- [The Taggart Institute](https://youtube.com/playlist?list=PLlfDtLAF5S2TxsYLKCNppHaHYMPafzfiu) has a collection of VODs where they build up a homelab live on Twitch
- [Ben Heater](https://benheater.com/free-training-resources-cybersec-and-it/) has a lot of stuff on his blog about building up a homelab and learning about networks so that's probably the most useful other thing I have
- Lab Ideas
	- [GOAD](https://github.com/Orange-Cyberdefense/GOAD) and [BadBlood](https://github.com/davidprowe/BadBlood) can both be used to set up or inspire Active Directory designs
	- Set up a Web Server, Git Server, and get some CI/CD going, automate all the things
	- Literally anything that you would find useful in your day to day. You don't always need to create a full network, maybe you just have one server at home that's useful to keep as a media storage server or something else.

### Pentesting
- [TryHackMe](https://tryhackme.com) is an online platform that will teach you how to hack from the ground up. It's how I got my start, and I am indebted to it. 80% of the content is free, and the subscription is only ~~$8 a month~~  $11.20 a month with the student discount.
- [picoctf](https://picoctf.org) is an introductory CTF (capture the flag) that gives you some puzzles to solve. They're not all pentesting related, but I didn't have a better place to put this, so here we are.
	- That said, some of their newer challenges even cover topics like Browser Exploitation, so I wouldn't neglect this even if your
- [HackTheBox](https://hackthebox.com) is where you put your pentesting skills to the test. It's a very "Try Harder" attitude, meaning I would **NOT** recommend it for beginners. However, if you want to put your CTF skills to the test, go for it.
	- If you are willing to spend $8 a month (student subscription), [HTB Academy](https://academy.hackthebox.com) gives you access to their entire penetration tester path (CPTS) for their certification. Whether or not you do the cert is up to you, but the course material is about as good as OSCP, a cert that is almost essential to break into the offensive security job market.
- [VulnHub](https://www.vulnhub.com/) is where you can download vulnerable virtual machines to hack. (**Edit 9/22**: VulnHub doesn't seem to be getting much love these days, seems they've been taken over by [OffSec Proving Grounds](https://www.offensive-security.com/labs/) but I'm not totally sure)

### Blue Team + DFIR
- [Blue Team Labs Online](https://blueteamlabs.online) is a gamified learning platform for defenders to practice their incident response and forensics skills
- [letsdefend.io](https://letsdefend.io/) is another platform that has some free content to help build SOC related skills
- [Cyberdefenders](https://cyberdefenders.org/) hosts a variety of new and old forensics challenges that are helpful to work through investigations
- [Tracelabs](https://www.tracelabs.org/) is crowdsourced OSINT (open-source intelligence) to find missing people, but they also have a lot of good investigative stuff to learn about.
- [13Cubed's Forensics Youtube](https://www.youtube.com/watch?v=VYROU-ZwZX8&list=PLlv3b9B16ZadqDQH0lTRO4kqn2P1g9Mve&ab_channel=13Cubed) highlights a number of tools and techniques to make the most of whatever forensic artifacts you have, and has very good nuggets of information
- [Boss of the SOC](https://cyberdefenders.org/labs/15) is a team-based CTF where you use Splunk (google it) to conduct investigations

### Web Apps
- [OverTheWire: Natas](https://overthewire.org/wargames/natas/) is like Bandit, but now you learn about some basic web vulnerabilities
- [OWASP Top 10](https://owasp.org/Top10/) represents the most common web app vulnerabilities and is a standard for web app security. You can practice these attacks in [OWASP Juice Shop](https://tryhackme.com/room/owaspjuiceshop), which is the TryHackMe version where you can deploy an instance to work with.
- [PortSwigger Web Security Academy](https://portswigger.net/web-security) is a free set of web application security labs made by the same people who made BurpSuite, which is the de facto tool when dealing with the web.
- [HTB CBBH Course](https://academy.hackthebox.com/preview/certifications/htb-certified-bug-bounty-hunter) is a course that builds up to a certification from HackTheBox that **means nothing**. However, the course content is put together very well and focuses a lot on bug bounty (mitigations are mentioned, though), and with a student email, only costs you $8 a month to get full access to (very good deal!!!!).
- Get a Raspberry Pi and build your own web server. Plenty of resources and tutorials on Github and Youtube for that. One of the best ways to learn how to secure something is to build and maintain your own infrastructure. (Or you could just build the webapp locally but homelab is cooler)

### Mobile/Wireless Security
> There are certainly more resources out there, I just need to find them
{.is-warning}

- I know Wireless Security (the class) teaches some mobile and wireless stuff
- [ippsec](https://www.youtube.com/watch?v=xp8ufidc514) has a video on how to set up Android emulation so you can dynamically analyze an Android app
- [HackTheBox's blog](https://www.hackthebox.com/blog/intro-to-mobile-pentesting) has an article explaining some basics
- [HackTricks](https://book.hacktricks.xyz/generic-methodologies-and-resources/pentesting-wifi) has a fairly lengthy compiled list of things
	- Mostly stolen from [SpectreOps](https://posts.specterops.io/modern-wireless-attacks-pt-i-basic-rogue-ap-theory-evil-twin-and-karma-attacks-35a8571550ee), [GDSSecurity](https://github.com/gdssecurity/Whitepapers/blob/master/GDS%20Labs%20-%20Identifying%20Rogue%20Access%20Point%20Attacks%20Using%20Probe%20Response%20Patterns%20and%20Signal%20Strength.pdf), etc
- **TODO:** More mobile stuff
### Reverse Engineering and Binary Exploitation
> You can tell where my biases are
{.is-info}

- [Malware Unicorn](https://malwareunicorn.org/#/workshops) has a few free workshops to go over some basics and advanced techniques related to reverse engineering and malware analysis
- [ost2.fyi](https://p.ost2.fyi/courses) also has a good amount of assembly/debugging courses, but I haven't done any of them (I've heard good things)
- [Pwn.college](https://pwn.college/) is an online course (run by Arizona State University) that takes students through program functionality, Assembly, debugging and reverse engineering. Each module has videos and practice problems.
- [Nightmare](https://guyinatuxedo.github.io/) is an online course focused around CTF challenges to teach different RE concepts. Each challenge has a writeup and can be 'solved' using open-source (free) tools.
- [ROP Emporium](https://ropemporium.com/) is a set of 8 challenges to teach you advanced techniques in stack-based buffer overflows. 
	- [HeapLAB](https://www.udemy.com/course/linux-heap-exploitation-part-1/?referralCode=362370A773BCC3F2730A) is a paid course on Udemy taught by the creator of this website to teach heap exploitation. If you really liked learning about buffer overflows and want to continue this path, highly recommend picking this one up.
- [LiveOverflow's Binary Exploitation](https://youtube.com/playlist?list=PLhixgUqwRTjxglIswKp9mpkfPNfHkzyeN) are video walkthroughs from learning C to doing heap exploits.
- [CryptoCat's Intro to BinExp](https://youtube.com/playlist?list=PLHUKi1UlEgOIc07Rfk2Jgb5fZbxDPec94) series covers basic stack based buffer overflow stuff that you might find in a CTF. I think it could be a little more technical (would not recommend if you know nothing about C/assembly), but the later videos are great explanations of some more
- [ir0nstone notes](https://ir0nstone.gitbook.io/notes/) are good bite-sized reminders of certain concepts
- Also Computer Security 2 teaches the basics of this

#### Malware Analysis
- [Practical Malware Analysis](https://www.oreilly.com/library/view/practical-malware-analysis/9781593272906/index.html) is a book that we have access to from O'Reilly, highly recommend to learn some of the basic techniques. Some of the tool info is outdated, but the principles and methodology apply
- [Practical Malware Analysis and Triage](https://academy.tcm-sec.com/p/practical-malware-analysis-triage/?affcode=770707_llmpidil) is a course taught by HuskyHacks and is the best introduction to malware analysis. It is well worth the $30 for the course.
	- **UPDATE 7/1/23**: TCM Security has now moved to a subscription model which is $30 a month, which can kind of be a steep price if you don't finish it in a month.
	- The first half of the course is available on YouTube here: [youtube.com/@HuskyHacks](https://www.youtube.com/watch?v=qA0YcYMRWyI)
- [OA Labs](https://www.youtube.com/@OALABS/videos) has a lot of high quality reverse engineering guides/lessons, primarily focused on malware analysis

### Cryptography
- Take Encryption at Lewis
	- [Serious Cryptography](https://www.oreilly.com/library/view/serious-cryptography/9781492067511/xhtml/cover.xhtml) is the book used in that class and its pretty good, you have access to this via Lewis (O'Reilly)
- [CryptoHack](https://www.cryptohack.org/) is a gamified platform exclusively for cryptography and cryptanalysis. Very, very smart people over in their Discord - 100% recommend this.
- [CryptoPals](https://cryptopals.com/) is a collection of cryptography exercises which are very programming-oriented (e.g. making you write your own frequency analysis, writing a padding oracle attack)
- [MysteryTwisterC3](https://mysterytwister.org/home/) focuses more on crypto design and didactical aspects (like a tutorial in differential cryptanalysis) and it includes many challenges with historic or "obscure" procedures and machines (like HandyCipher or Sigaba).
- [Dan Boneh's Cryptography Course](https://crypto.stanford.edu/~dabo/courses/OnlineCrypto/) is another resource to learn the subject
- Books - if you want to dig deep
	- [An Introduction to Mathematical Cryptography](https://link.springer.com/book/10.1007/978-0-387-77993-5) is organized like a traditional math textbook and dives much deeper into the math behind asymmetric crypto (costs $50 but you can "find" it)
	- [A Graduate Course in Applied Cryptography](https://toc.cryptobook.us/) - haven't read this one, but also have heard good things, probably best to read the previous book first

## Law and Policy
**TODO** sorry!

## Misc
- As a Lewis student, part of your tuition is going to an [O'Reilly Subscription](https://learning.oreilly.com/profile/) - **USE THIS**
	- One series of books that's good is the entire [No Starch Press](https://nostarch.com/) line, they publish great books on a wide variety of security topics
	- There's also some instructional and cert prep books outside of it that are definitely worth looking into. Some suck, others are great.
- **TODO**

## Content Creators
There's plenty out there, but I will list my favorites here (warning, heavily biased towards my own interests):
- [John Hammond](https://www.youtube.com/channel/UCVeW9qkBjo3zosnqUbG7CFw) is currently a Senior Security Researcher at Huntress Labs who makes content on YouTube on the side. Whether it's a CTF writeup, mini-documentary series, or just a livestream VOD, John is very friendly and explains concepts well, both hacking and forensics. He also just knows a LOT.
- [LiveOverflow](https://www.youtube.com/c/LiveOverflow) is just a gold mine of information. His specialty is the binary exploit and hardware side of things, but he has videos on all areas of hacking, and more specifically, exploit development research. He also attends probably the biggest hacking events, so that's interesting to watch.
- [ippsec](https://www.youtube.com/c/ippsec) and [0xdf](https://0xdf.gitlab.io/) are architects over at HackTheBox that develop lab content and also do writeups for the boxes on the platform. Although it's more CTF/Hacking content, both do an excellent job of diving deeper into the root cause of the security issues they find, which is helpful from the defender's POV.
- [mttaggart](https://twitter.com/mttaggart) - A great instructor on both the defensive and offensive stuff, streams regularly on Twitch, and has an excellent series of VODs on setting up a homelab.
- [LaurieWired](https://www.youtube.com/@lauriewired) is a reverse engineer that tends to focus more on mobile, but still produces high quality walkthrough videos on YouTube
- [Alh4zr3d](https://www.youtube.com/channel/UCz-Z-d2VPQXHGkch0-_KovA) is a red teamer in the field who streams on the side, and I've really liked his stuff lately. Take the energy of a popular streamer, make him a hacker, and now you have Alh4zr3d. 
- [Malware Unicorn](https://malwareunicorn.org/#/) has a bunch of amazing reverse engineering and malware analysis content on her site, Twitter, and Github, including a bunch of mini-workshops that are just free to go over.
- [Marcus Hutchins](https://www.malwaretech.com/) - Former black hat who stopped wannacry and now does consulting and writes good blogs on his website related to red team tactics
- [VX-Underground](https://twitter.com/vxunderground) is a non-profit organization that has a large vault of malware and also posts security news on their twitter about new developments and leaks.

Other ones that I know are good but don't want to dedicate a whole paragraph to.
- [Azeria aka Fox010](https://azeria-labs.com/) - Mac OS and ARM architecture is an area that can definitely slip past your radar, but Azeria has produced so much work on the internals and reverse engineering of this stuff
- [Vickie Li](https://vickieli.dev/) - Awesome blog, application/web security focused (doesn't post that much as of 1/10/24)
- [CryptoCat](https://www.youtube.com/c/CryptoCat23) - Very good CTF writeups, also has a good voice, has recently pivoted into video game hacking
- [chompie1337](https://twitter.com/chompie1337) is an exploit dev researcher with a fire Twitter. Tell me [this tweet](https://twitter.com/chompie1337/status/1531064276915568645?s=20&t=AFLTY_r-QReR8dT2hSGkgg) isn't funny. But also very, very proficient with low level stuffs.
- [HuskyHacks](https://huskyhacks.dev/) - Red teamer who teaches a malware analysis and responsible red teaming course, also cool person.
- [c3rb3ru5d3d53c](https://twitter.com/c3rb3ru5d3d53c) - Malware analyst that streams a lot of her work and regularly publishes tooling to GitHub
- [Darknet Diaries](https://darknetdiaries.com/) - THE infosec podcast that everyone talks about


Hopefully I can grow this list a little bit more over time (I'd like to make it more diverse but it's hard to find people who are producing stuff consistently), so let me know if there's anything you think I should add to this list!
## Discord/Slack
What were once IRC chats have now manifested in many, many discords. If you want to chat with professionals or other people in your position, I *highly* recommend checking out some infosec discords.

- [Taggart Institute](https://discord.com/invite/VQfyzB62H3)
- [Black Hills Information Security](https://discord.com/invite/bhis)
- [Infosec Prep](https://discord.com/invite/infosecprep)
- [TryHackMe](https://discord.com/invite/tryhackme)
- [HackTheBox](https://discord.com/invite/hackthebox)
- [TCM Security](https://discord.com/invite/tcm)
- [John Hammond](http://johnhammond.org:8080/discord)
- [Cyber Jobs Hunting](https://discord.com/invite/x543cfPE5w)

Don't try and join every single one possible. It's much more beneficial to eventually try and find what community feels best for you.

## Conclusion
I hope this wasn't too overwhelming, I know it's a lot. But, now that you have a baseline of where to start from, just go for it! Start with TryHackMe for sure, but branch out in whatever way you feel like. You get out what you put in. 

Good luck out there! You'll do great!
Happy hacking (or defending)! o7