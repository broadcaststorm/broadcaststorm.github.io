---
layout: post
title: Why fundamentals matter
date: '2016-04-06T22:06:28+00:00'
tags:
- monitoring
- power outage
- IT
- fundamentals
tumblr_url: https://broadcaststorm.tumblr.com/post/142381760438/why-fundamentals-matter
---
With the wrap up of March Madness and the inevitable withdrawal symptoms beginning, let’s focus on fundamentals of a different sort. This post will set up the scenario and discuss the generic IT fundamentals in monitoring. &nbsp;The next post will look at some network routing fundamentals.

## Back story

A few months after making the official move into a senior network engineering role [see beginnings of this blog for back back story on that], we experienced a site wide power outage. &nbsp;Both primary communications closets are UPS and generator backed as was the data center. &nbsp;Monitoring of switches, ISP uplinks and hosts in the DMZ (with public facing website) all showed green and were thought to be safe …

Until the closet A lost power 15 minutes into the blackout. **What?!?!**

“Okay, not good. &nbsp;But, fortunately we have the secondary closet.” All the switches and remaining links shown green. Someone find a car (we were 5 minutes offsite) and go check out closet A. &nbsp;Joe heads out the door.

Then, 5 minutes later, systems team walks in saying that the DMZ and internal DC networks can no longer connect to the Internet. &nbsp;Cell service works and we can’t connect to our website. &nbsp; **What?!?!** &nbsp;

Monitoring hasn’t changed since closet A went offline and devices/links up are still up. &nbsp;Lots of discussion ensues.

10 minutes later (25 minutes into blackout), closet B loses power. Okay, screw why we can’t get to the website. &nbsp;We have no Internet connections now so it’s all moot.

10 minutes later (35 minutes into blackout), closet A power is restored. Switches online. Internet online. Website online. Joe reports back - generator was running but surge from power failure tripped breaker and UPS was not getting power.

10 minutes later (45 minutes into blackout), closet B power is restored. Joe took facilities guy to closet B and found same issue with generator and UPS.

Many hours later, site power restored, breathing resumes.

## Lesson #1 - Monitoring the easy things is easy. Monitoring the right thing is important.

The generators were too old to have any means of remotely monitoring them directly. Upon site-wide power failure, facility personnel have standing procedures to drive the site and verify the generators are operational. And they did — By driving up to the building, rolling down the window, hearing it running, and moving on to the next generator.

The circuit trip that Joe reported was a new surprise as it had never happened before. The entire site losing power is a _ **BIG DEAL** _ with a lot of safety issues that come with it. People are rushed because (rightly so) people safety comes first.

Okay, live and learn - continual process improvements with facilities who, by the way, were extremely apologetic and eager to modify their process. They now check the output in addition to making sure its running.

## Lesson #2 - Monitoring is of little use if it is not reliable. Monitoring that is automated tends to be more reliable.

Having a human, “manual”, process was a crucial component to ensuring a functioning generator was operational in a power loss scenario especially since there was no electronic means of remote communication.

But, what about those UPS units? &nbsp;They were only 2 years old … and both were equipped with SNMP-based monitoring cards.

Unfortunately, neither management card was set up or actively monitored. “Loss of input power” would have been an important alert to receive during the outage while the UPS still had battery charge.

Automated monitoring does not stop to weigh the priority of checking a service versus taking care of public safety. It has one dedicated function that runs continuously and reliably.

## Lesson #3 - Condition green does not necessarily mean all is well.

When primary closet A failed, all the critical links in secondary closet B showed green on the board (except for connections to closet A). &nbsp;We had connectivity to our secondary peer. &nbsp;We had connectivity internally. &nbsp;Yet, “the Internet was down” or, more fatally, our website was offline.

Oddly enough, it wasn’t an automated process that revealed the website was down. Systems monitoring had shown it to be all green as well. (IIRC) Someone from outside IT noticed it down and contacted someone they knew in IT.

Services that are not monitored frequently lead to assumptions that it is always working when something else is working. If the link to the secondary ISP is up, routing to the Internet must be working… except when it is not.

## Lesson #4 - Mistakes happen. Learn from them. “Always leave your site better than you found it.”

My last statement in lesson #3 says it all. Most of our monitoring was actually fairly comprehensive. But, as we all run the risk of doing, you can spend 90% of the time on the last 10% of the project over-engineering a service that then increases its cost by 90%.

Okay, I made those numbers up. The point though is…

Monitoring comprehensively is time consuming and, without the right tool, could lead to a large number of alarms being triggered by a single event. Monitoring that is too noisy can be ignored (unmanage it all!) or, more fatally, the real problem can be lost in a sea of alarms from dependent services. Anyone who has dealt with IDS/IPS can preach a sermon or two about that.

So, here’s a strategy to tackle the right balance in monitoring:

1. Monitor as many aspects of a service as you reasonably can when deploying it.&nbsp;  
2. Have a peer review of your service and monitoring with your technical lead or a colleague.&nbsp;  
3. Discover what else should be monitored and add it to the project  
4. Identify what metrics reasonably should not be monitored. &nbsp;  
5. Document those unmonitored metrics and the reasons!  

Usually, when you have to put justification in writing, your intuition quickly informs you whether you should really put in the effort to monitor something or whether it seems safe to defer it.

But, keep in mind, we are all under deadlines. The caffeine isn’t always firing all cylinders for you or your peer (blame the all-hands meeting). Something will be missed, forgotten, or your reasoning for not monitoring it will be wrong.

You most likely discover the truth during an outage. When you do, leverage your documentation as best you can, fix it, and notch another “continual service improvement” metric in your functioning ITIL operations. &nbsp;

## Stay tuned for the next blog…

“How the Internet went down” or a crash course in the behavior of Cisco IOS routing tables

