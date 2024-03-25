---
title: Getting a Job in 2024
description: Note that, as of writing, most of the stuff here is from my own (i.e. one person's experience). I hope that I can modify this to include other people's thoughts later.
published: true
date: 2024-03-25T02:03:35.824Z
tags: 
editor: markdown
dateCreated: 2024-03-25T02:03:35.824Z
---

# Finding a Job in 2024

> **Context**: I recently got a job and recently messaged a person for 30 mins straight on how to find a job in infosec. Needless to say, I figured it was best to document a good chunk of what I talked about here, where anyone can see it.

## Intro
In 2024, finding a job in tech is hard. This write up is meant to answer a lot of the common questions that come up surrounding finding a job in infosec, but is by no means comprehensive nor representative of everyone's experiences. The job market is extremely dependent on current trends in the industry, and everyone's job hunt goes differently. However, the topics I plan to cover here (apart from my own job hunt experience), should be relevant for a while (I hope).

There are also some biases that I'll state now (prone to change if someone else contributes here)

- There were very specific jobs I was looking for (i.e. hands-on keyboard type, SOC analyst, Penetration Tester/Red Team, Threat Hunter).
- I am not very familiar with the state of GRC/compliance or more administrative roles or what that looks like in the first place.


## TL;DR on My Path
![jobsearch.png](/jobsearch.png)
<sup>note that I did get the offer after the 4th interview :)</sup>

I started applying for security jobs back in late December 2023 to early January 2024, and concluded my job hunt at the beginning of March. I was primarily looking for offsec roles, but was open to doing more defensive stuff like SOC work or AppSec if I was really desperate.

I cast a bit of a wide net, but at the same time, I definitely skipped a lot of jobs that were in fintech/finance, military, etc. out of principle- I just didn't feel comfortable working directly for those institutions. Honestly though, I think prioritizing stability over how "ethical" the company might be the play, but that's entirely a personal decision.

## The Job Hunt
As someone who has a decent amount of technical ability, I had a very simple game plan:

1. Make a list of companies that I would like to work at (10-20 is a good size)
2. Check in with those companies every few days to see if any jobs were listed, and apply to the ones that interested me
3. If I hit at least 3 applications for the day, I felt good
4. If not, continue searching for job listings on LinkedIn, Indeed, and [infosec-jobs.com](https://infosec-jobs.com/) (infosec-jobs.com as a lower priority)

If you do 3 applications a day, that's 21 applications in a week, which adds up over time. Even if you do 1 a day, that's still 30 applications in a month. If that seems like a lot, there are ways to optimize the process:

- *Track your applications* - Whether it's a spreadsheet, [Kanban](https://github.com/mgmeyers/obsidian-kanban), or a journal, keep track of what you have and haven't applied to. It gives a much better perspective on what things look like.
- *Reuse what you write* - Applications can be tedious, so if there's stuff you know is going to show up on every application, keep a document where you can easily copy and paste stuff without having to always go back to your resume (e.g. Job Experience, Education)
- *Keep reusing what you write* - A lot of applications ask similar questions "Why do you want to work here?", "What stands out about our company?". Everytime you get an essay question, save what you write, and continue to improve upon your format and what you write. You'll find that you can reuse a lot (just be careful not to copy and paste company names)

> Whenever you find a job on aggregators like LinkedIn or Indeed, always try to apply on the company's website instead of through LinkedIn. More likely to get directly to the hiring manager and lets you check if the job is real.

As for actually searching for jobs, here are some of the best resources I found:
- BanjoCrashland from Black Hills Information Security has an excellent [playlist](https://youtube.com/playlist?list=PLqz80p7f6dFumNG0wU4Ql41PvhzamHO3_&feature=shared) on hunting for jobs more effectively. There's a lot of stuff here, but I *highly* recommend viewing this. Also join the [BHIS](https://discord.com/invite/bhis) discord while you're at it, they have a semi-active job board, announcements about live streams, and just a bunch of high-quality resources.
- Graham Helton's blog on [Becoming a Red Teamer at Google](https://grahamhelton.com/blog/jobs/) is probably the most accurate representation of finding an offsec job right now, and a good read.
- Jason Haddix's blog ["A Hacker's Guide to Finding Cybersecurity Jobs"](https://www.jhaddix.com/post/a-hackers-guide-to-finding-cybersecurity-jobs) is a bit dated, but has some good fundamentals you can apply

## The Resume
The idea of a "perfect resume" will be debated for eternity and what I say here might completely contradict what someone else says. Regardless of people's opinions, **the resume is what gets you in the door** if you don't have connections. Hiring managers are forced to look at what you write about yourself, and have to make a judge of character off of it, so it's naturally a hard thing to do. Here are some pointers:

- Always try to tailor your resume to what the job listing has in the description. If the job explicitly mentions `"Experience in creating security tools and understanding underlying programming languages (such as Python, C#, C/C++, Rust, Nim or similar)."`, you should not only be namedropping the languages that apply to you, but bringing up projects where you've applied those skills. One tip is to have a general resume for each type of job you're applying to, and then make a copy of your template to add or remove stuff as necessary.
- With the tailoring in mind, you should frame your resume to tell the best narrative of why you should be employed there. You genuinely have to look at your resume and ask yourself what makes you different from anyone else you see at competitions or networking events, and how you can sell that.
- Many companies use automatic scanners (aka ATS) to pick up on keywords and automatically filter on it. You can find sites that try to do this online to verify that the information being pulled out of your resume is the way you want it to be seen- don't want to miss out on opportunities because of a bad template
- The 1 page vs 2 page argument will exist until the end of time, and this will entirely depend on the company. I want to say in most cases (and this is coming from someone who is still very new), the companies that actually care about who you are will take the time to skim through 2 pages as long as the information you've included is worth including. If you're struggling to hit a page and a half or feel like you're adding fluff for the sake of making it longer, trim it down. Nobody will ever be upset at 1 page, but if you feel like you have enough to bring to the table and talk about with 2, I'd send it.

## The Interviews
Interviews can either be the most stressful thing or the most exciting thing, for multiple reasons. Good interviews should not be like oral exams you'd do in college or high school, they should be an opportunity for you and your interviewer to get a better understanding of each other, and for the interviewer to get a sense of your technical abilities.

As far as the technical element goes, you can find a bunch of questions online, but my two go-to resources are:
- *[Daniel Miessler's Questions](https://danielmiessler.com/p/infosec-interview-questions)* - Although this is from 2019, these questions still hold up and are a good mix of baseline info (e.g. "What is the difference between symmetric and asymmetric encryption?") and deeper, more philosophical questions (e.g. "What is the goal of information security?")
- *[LetsDefend's SOC Questions](https://github.com/LetsDefend/SOC-Interview-Questions)* - More of your typical set of questions, but this one just has a good spread of foundational computing questions and security/SOC-focused ones

If you feel like explaining technical concepts isn't something you can confidently do, **that is something you need to work on**. You don't even need to explicitly practice this in an interview format- try being the one to step up and give presentations at club, help people with their homework or explaining difficult concepts, etc. You probably know a lot of stuff, but knowing how to break it down concisely and precisely is an art that takes time to develop.

I also understand that a lot of people that are computer/science-heads can have a hard time communicating in general. If this is you, my best advice is to honestly (1) go talk to more people in person and (2) go watch interviews, shows, and podcasts of the people/celebrities that you think are the most charismatic and in touch with the world (i.e. don't say "pogchamp", "skill issue", or make niche internet references to people not in touch with the culture) and figure out what they do, how they do it, and why it works.

## Aside: On Certifications
I, and many others, would like to imagine that having a degree that says you ~~googled~~ studied for about four years should be enough to prove technical ability, and sometimes, it is. 

## Aside, again: Levelling Up and Networking
