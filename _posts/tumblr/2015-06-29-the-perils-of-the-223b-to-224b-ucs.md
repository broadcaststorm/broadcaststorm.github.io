---
layout: post
title: The perils of the 2.2(3b) to 2.2(4b) UCS upgrade...
date: '2015-06-29T21:24:54+00:00'
tags:
- ucs
- upgrade
- 2.2(3b)
- 2.2(4b)
- fabric interconnect
tumblr_url: https://broadcaststorm.tumblr.com/post/122810727338/the-perils-of-the-223b-to-224b-ucs
---
In short, you want to work with your account team’s sales engineer to secure you a great TAC engineer prior to upgrading. You may not have the issue we did but you will be very thankful to have TAC locked and loaded if you do have issues. &nbsp;Punch line - for our two domains, we rebuilt each and every fabric interconnect (separately). &nbsp;Great news - no user impact to services running on those domains!

First, a quick overview of our setup. We have UCS Central 1.3 that provides all the common policies that are shared among our UCS domains. We do not have any global service profiles or service profile templates. We opted for creating those locally on each domain (perhaps a post for another time).

We have two UCS domains connected to that UCS Central. Both domains are 6248UP fabric interconnects (FIs) with various number of 5108 chassis and 2108 I/O modules. Both were running the 2.2(3b) infrastructure code.

On the smaller domain, we kicked off the “Auto Firmware Install” for the infrastructure. Everything went smoothly until the subordinate FI (fabric B) rebooted as part of its upgrade. It never came back from the reboot. Its management port was down and the peer FI (fabric A) could not communicate with it through the L1 and L2 links.

After a drive to the data center and connection to the console, I discovered the FI was prompting for console or GUI configuration as if we were deploying a brand new FI. In a normal setup, you enter “console”, the FI detects its peer, prompts to join the cluster, and you are on your way to rebuilding the cluster.

Not in this case: Error messages about ‘grep’ commands appeared and we were prompted to specify setup mode: new install or restore. We all know what that means - download your tech support and open up a TAC case. Since I had a FI down on a production unit, that is a severity two (online to create the case as severity three, then phone call to escalate it).

Long and short of it, the first engineer I received concluded the software load was corrupted and we commenced with re-initialized the appliance and rebuilding it from scratch. We got the process right but used the wrong code versions. His shift was over so he handed off to another engineer and he immediately spotted the mistake. We were good to go.

Meanwhile, back in “firmware auto install” land, the primary FI (fabric A) was waiting for the subordinate to return. &nbsp;When it did (because we rebuilt it so nicely), the process re-engaged and upgraded the subordinate (fabric B) to the new code and activated it. &nbsp;This time, correctly. &nbsp;The subordinate was intact and all set for failover. &nbsp;Fantastic! &nbsp;

Except, when the primary was activated, it came back corrupted as well. &nbsp;Again, the failover worked correctly so no impact to services. &nbsp;Commenced rebuild of FI (fabric A). &nbsp;However, when we rejoined it to the cluster, the process noticed the subordinate FI being added (fabric A) was older code and prompted for upgrading. &nbsp;I of course said yes. &nbsp;The rejoining process essentially completed the firmware upgrades and activation for the&nbsp;“firmware auto install” process.

On to the second, larger domain – Based on information from TAC, namely manual installation provides better control in case of a failure, we decided to manually upgrade the firmware for the infrastructure components of our second domain. &nbsp;This process is called&nbsp;“Directly Upgrading Firmware at Endpoints” found at:&nbsp;[http://bit.ly/1GLGKZQ](http://bit.ly/1GLGKZQ)

As my opening monologue indicated, the process did not save us from rebuilding both FIs exactly as we did with the auto install process.

When we were all said and done, our TAC engineer found the correct TAC case/bug/defect search term combination and it would appear we were hitting the following issue: [CSCut63966](https://tools.cisco.com/bugsearch/bug/CSCut63966). &nbsp;We may not have needed full rebuild of the FIs after all. &nbsp;

Important information for your planning purposes - to rebuild a FI from corrupted state and get to a&nbsp;“operable”, it takes about 3 hours once you begin. &nbsp;By operable, I simply mean it is back up in a state where it is passing traffic. &nbsp;If all you need to do (according to the bug listing) is manually boot the correct firmware from the “loader\>” prompt, that could take under an hour. &nbsp;

I hope this bit of experience helps better prepare someone out there for any surprises in their upgrades. &nbsp;I know I definitely got proficient at playing around the bowels of the FI (debug\_plugin is cool!), especially for a Linux guy.

For your future reference, the link for [Cisco UCS Manager Documentation](http://bit.ly/1RNOT5j).

