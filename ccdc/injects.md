---
title: Injects
description: 
published: true
date: 2024-02-22T15:48:31.337Z
tags: 
editor: markdown
dateCreated: 2024-02-22T06:12:29.862Z
---

# Injects

## What are Injects?
Injects are "business tasks" that you'll have to complete throughout the day that can either be strictly business-related (e.g. Business Memo to CISO that everything is fine, Write an Acceptable Use Policy) or technical (e.g. Install X, Y, and Z and show proof). Functionally, they serve two purposes:

1. (What they want you to believe) A way to practice real-world, interpersonal communication skills. You'll need to work with your team and convey your ideas to the fictional administration that you work for.
2. (The Reality) It's a distraction. You're getting hammered by red team, and will lose points the longer your services are down, but you'll also miss out on points for not doing injects.

And that's the real dilemma here. If you focus injects, your services will get hacked and you'll be panicked the whole time. If you skip injects, you can have perfect uptime but still miss a podium finish because injects make up at least 40% of points. **The balancing act of scoring injects but also knowing when to let things go is HARD**, and you shouldn't let it weigh on you too heavily.

## Strategy
- **Prepare policy templates/documents ahead of time!** You should not have to write an Acceptable Use Policy or Disaster Recovery plan during the competition. These are things that can be prepared ahead of time for you to just copy and paste in.
- **Prepare certain technical changes ahead of time!** I can promise you they will always ask you to set up (1) login banners, (2) a password policy, and (3) an asset inventory document. Again, these should not be things you're doing from scratch. Automate these things before so you don't have to waste 15 minutes trying to get banners on everyone's systems.
- **Communicate, communicate, communicate.** Someone should have the inject board up at all times and tell people what's coming up and when. Hopefully the team chemistry is good enough that you each know what person should do what inject. Don't give the Windows guy the Network Traffic inject, and don't give the Firewall guy the Active Directory injecct.
	- It's one thing to have good comms, but you also need to act on those comms too. Don't just say something and let it be ignored. If it's urgent, make sure it gets addressed.
	- It may be worth planning a seating arrangement ahead of time. For instance, if I have two Linux people, where one is a veteran and it's the other's first time, keep them sitting together for easier communication!
- Despite their point values, **not all injects are built equally**. One inject might ask you to add a user account, and another might ask you to scan the whole network, but both might only be worth 100 points. You can get the user account done in < 5 minutes, but the network scan might take longer. **Know when to bail out, it's okay to miss a few**.
	- That being said, top scoring teams get almost every inject, so it's still crucial to go for them.
- **Lie, cheat, and steal (not to be taken literally).** Imagine you need to show that you've run a Docker container, then updated it, but, oh no! You don't have enough space to show the updated image, and your machine is being frustrating. **Photoshop your screenshots**. Unless they're coming around in-person to verify what you're doing, there's no reason not to do this.
	- Injects are, ultimately, just distractions, so don't feel bad about this. Stay within the rules, but it's okay to say you've done something when you haven't. Keep the fibs reasonable. Stay chill about it, there's no penalty.
	- Going to add one more note here, **don't completely lie about a task**. If you haven't put password policies on *every machine*, but got most of them and are running out of time, saying you did it on every machine will not kill you.
- **It's better to have something over nothing.** If you've half completed an inject and you're running out of time, just submit it! Points are given on a spectrum, so scoring 30-50 points is better than no points.

## General Tips
- **Peer Review Documents.** It's very easy to get lost in the heat of the moment and write a bad memo. If you have the time, try and get one other person to read and "sign off" on the document before sending it. You'd be surprised with how many typos you can add on accident.
- **Presentation over all else.** It's equally important to have strong content and good writing/presentation, but if I have to choose one over the other, presentation > content. Don't let the technical guy write the inject, especially if they barely passed English 101. Make it look professional, the white team will, generally speaking, be more inclined to give a well-formatted inject more points than one that is a mess of screenshots that has zero professionalism.
- **Keep track of how long you're doing an inject.** You're being hacked! Generally speaking, unless this is supposed to be a long inject, you really shouldn't be spending longer than an hour, maybe even 30 minutes on any inject. There can be 20+ injects in any event, so timing is important.
- **Avoid Excessive Jargon**. If the inject asks for technical documentation for something, feel free to use jargon. But, in most cases, if you don't have to use jargon, try to avoid it. For instance, instead of saying "local privilege escalation vulnerability", say "a low-level user can gain administrator access".

## Examples
### [DOCKER01] Assess and plan any necessary updates to Docker
We need to validate whether we have a vulnerable version of Docker running. Recent security events, it’s essential that we ensure that our infrastructure is kept up to date.
Provide the output from docker version and your plan to upgrade (if necessary) to a
currently supported version.

### [BAN01] Legal Disclaimer for Network Access
Configure features in the network equipment and servers to issue a warning message prior to anyone logging in. Best practices standards are for the text of the message to include:
1.) A clear admonition that only authorized users are permitted, there is no general
welcome.
2.) All network activity is being monitored and logged.
3.) That activity logs and other monitored information can be used to investigate and
prosecute unauthorized access.

Develop a management memo that clearly lists the text of the message that is being
used, so that it can be reviewed by the legal department.
Screen shots for each device, showing the configured message functioning.

### [MGR02] Respond to Your Manager
You are the lead of the infosec team. Your Director is asking for an URGENT update. They need to
know your current roadblocks and what you have done to solve the problem to this point. Give your manager all the information that they need before they go into the Presidents office to provide an update. Don’t let your Director down, they are depending on you!! Be sure to answer to your audience.
Please be sure to include:
1.) Current roadblocks
2.) What the team has done to solve the problem to this point

### [EXE01] Risk Assessment of Vulnerabilities
After completing the Nessus scan please provide an executive summary that would include all of the work stations and servers and deliver an Executive Summary listing all servers and work stations.