---
layout: post
title: Assuring your network will do what you think it will do
date: '2017-05-11T12:28:11+00:00'
tags:
- NFD13
- ForwardNetworks
- NetworkAssurance
tumblr_url: https://broadcaststorm.tumblr.com/post/160553653183/assuring-your-network-will-do-what-you-think-it
---

- “Sweet! &nbsp;I’d love to be able to simulate a change on the whole network!”  
- “\*All\* paths from source IP to destination IP? Even ECMP?”  
- “Wait, they aren’t running VMs/containers of each device to simulate all this?”  
- “All of this can run on site, with one virtual appliance for the modeling engine?”  

## Cloaking Device Disabled

Forward Networks came out of stealth as a Silicon Valley startup company on Monday, 14 November 2016. &nbsp;So, if you haven’t heard of them, do not feel bad. &nbsp;Their initial press release, like many new product announcements, says a great deal and makes a number of claims. &nbsp;But, I didn’t really know what to expect.

Their exit coincided with their presentation to the Network Field Day 13 event ([http://techfieldday.com/event/nfd13](http://techfieldday.com/event/nfd13)). In that presentation, they provided very detailed demonstrations about the product - showing what you can do and how it can make your operations much more streamlined. &nbsp;Side note: if you love demonstrations and engineers interrogating engineers about product features, you are going to love the Forward Networks videos. &nbsp;This was a product in which practically every delegate had some interest.

## Network Assurance

The Forward Platform is described as a network assurance product but what does that mean? &nbsp;Even their website was a little vague on the matter. &nbsp;Simply put they break the concept of network assurance down to two specific categories: correctness and performance.

The latter (performance), they did not focus on (today) although they did mention customers are definitely interested in how current performance impacts network operations. As an example, performance assurance would answer the question “With my current traffic loads, will my connectivity to the DR site be able to handle a failover event?”

The former category - correctness - is very much the focus of their initial product launch. &nbsp;Given the business intent, are all the network devices currently configured and operating to support that intent? &nbsp;For example, does web traffic from the Internet pass through my load balancers and firewalls to reach my web servers? &nbsp;Even if I take down one load balancer? &nbsp;Or firewall? &nbsp;Or both?

The most intuitive aspect of the software to me was defining the business intent. &nbsp;Business intent as they talk about it is posing questions to the Forward Platform. Back at the office, this really translates to test cases - such as monitoring rules you set up. &nbsp;The types of monitoring you would put into SolarWinds or Nagios or filters for Wireshark, for example, are the same concepts/actions you used to define intent.

Except, you can also validate prohibited traffic - no DHCP traffic should reach the web server. &nbsp;Web traffic should not reach the database server. Etc.

So, upon first deployment, you might have to allocate some time to defining as much (or as little) as your business intent as you want. &nbsp;For a few scenarios, you will find in the demonstrations that the web interface is very lightweight and responsive - the session video ([https://www.youtube.com/watch?v=Zg0u9a4ZW7Q&t=11s](https://www.youtube.com/watch?v=Zg0u9a4ZW7Q&t=11s)) at Tech Field Day’s YouTube channel has an excellent walk through of how to create these checks.

Some highlights - the pre-defined checks that we all want (traffic from client can reach server) to some you necessarily wouldn’t think of (traffic flows on all links in an etherchannel). &nbsp;Those look like this in the web interface:

<figure class="tmblr-full" data-orig-height="862" data-orig-width="2856"><img src="/images/tumblr/tumblr_inline_opsqmhXk9a1re93or_540.png" data-orig-height="862" data-orig-width="2856"></figure>

Worried that defining all those tasks for your extremely large network could be daunting? &nbsp;Yes, Virginia - there is a Santa Claus and an API for the Forward Platform.

## Searching Your Network

Those intents and alerts that can be defined fall under the “Verify” aspect of the Forward Platform. &nbsp;Another aspect is Search - one that is immensely useful for your operations desk. &nbsp;Users complaining that they can’t get to the internal portal? &nbsp;(Cue announcer voice) Reduce your “mean time to innocence” with Search (End announcer voice).

Seriously, you can very quickly determine whether the network has a role in the issue by:

1. Getting the latest snapshot from your network - which collects the latest current configuration and running states from each device  
2. Use the search function to show the path between users and the service  

This capture from Brandon Heller’s demonstration ([https://youtu.be/\_\_iaT7WQ41w?t=4m36s](https://youtu.be/__iaT7WQ41w?t=4m36s)) really shows it all. With Google like adaptive search terms, you quickly get the current, live paths involved for the source and destination you are investigating:

<figure class="tmblr-full" data-orig-height="1318" data-orig-width="2876"><img src="/images/tumblr/tumblr_inline_opsqlteUdn1re93or_540.png" data-orig-height="1318" data-orig-width="2876"></figure>

As we discovered during questioning, this newly released product currently understands and models the physical “underlay” transport including VRFs - however, virtualization switches (VMware, e.g) and overlays such as VXLAN are coming soon. &nbsp;So, you might not see the complete “virtual last mile” as I call it but you will definitely get to the physical server hosting the VM in the case of vSwitch.

## Modeling versus Simulation

Those two capabilities are exceptional technologies that really do help operations teams with extremely complex networks - especially ones that can’t be simulated using virtual appliances in such environments as GNS3 or VIRL. As more overlays and SDN are introduced into the data center, even simulating a relevant subset of the network is becoming computationally difficult.

That’s where the Forward Networks approach of modeling excels over the virtual appliance simulation approach. By reducing your network to data structure that can be modeled effectively and efficiently, they can ingest your entire network across a large range of vendors (Cisco, Juniper, and Arista) and devices (switches, routers, load balances, firewalls, etc.) to then perform those search and verify functions.

## Prediction

As a network architect, though, the prediction capabilities of the platform were the most exciting portions of the demonstration. &nbsp;Ever wonder if this configuration change is going to break anything directly? &nbsp;That’s usually easy to know ahead of time in simple networks.

How about whether your change will interrupt service on a different part of the network? What about a change someone made that broke redundancy for some of your VLANs but not others?

Because the Forward Platform has the complete model of your network encoded into their data structures, they can also predict the behavior of your network under different configurations. &nbsp;Ultimately, they would love to be able predict based on current conditions as well - but more on that later.

In my experience, most shops of any size have a change control process in place - one that, in the ideal ITIL sense, would correctly state the actual impact to the production environment so that the risk, value, and timing of the change could be intelligently discussed. &nbsp;Without a tool such as the Forward Platform, you can say what you \*think\* will happen based on what you might know - and maybe even leverage actual configurations to provide a sound foundation for that analysis.

How many times have we been burned by “wait - it shouldn’t have done that?” or “who the heck put that configuration in there?”? &nbsp;In my opinion, this is where the serious value can be derived from this platform - the Forward Platform can pull the entire network configuration and run states on demand prior to simulating the change. There does not have to be a discrepancy between a stale configuration and what you are testing against.

An demonstration of the Forward Platform prediction software can be seen at this part of the product demonstration:  
[https://www.youtube.com/watch?v=\_\_iaT7WQ41w&feature=youtu.be&t=24m16s](https://www.youtube.com/watch?v=__iaT7WQ41w&feature=youtu.be&t=24m16s)

As you might pick up, the more validation or unit tests you have in your network, the better the “Predict” functionality will work for you. &nbsp;As they (Forward Networks) point out - it’s very much like a software development mindset: the more (meaningful) tests you embed, the more confidence you build. &nbsp;As I mentioned before, there’s a great number of pre-defined checks but you must implement them to gain that confidence.

## All is well but not perfect with Forward

Keep in mind - as I mentioned, this is modeling and not simulation. &nbsp;One of the first implications of that is the platform will not protect you from bugs in the switch/router/firewall code. &nbsp;Back end validation farm runs through as many code versions from as many vendors as they can. &nbsp;The impression is that they run through them “all” to validate their modeling engine correct represents them all. &nbsp;I’m not sure that is possible for all the resources that might be required to validate all protocols (routing, spanning-tree, etc).

As I also mentioned previously, the current platform does not take into account performance aspects of the network - although they did emphasize (without committing) that several customers expressed strong interest in that capability. I for one will be keeping an eye out for their future developments. &nbsp;Imagine the equivalent of integrating information from SolarWinds into a modeling and prediction engine - packet drops, interface counters, flow information.

Take flow information - say you wanted to know how web traffic for a certain customer would distribute across 2 more additional links? &nbsp;Stress those links because the customer preferred their DR site replication traffic over those links? &nbsp;You see the potential?

Before anyone takes any of that out of context - that is **me** brainstorming what might be. &nbsp;Forward Networks did not mention those examples, suggest those examples, hint at those examples, or commit to those capabilities.

## Wrapping It Up

So I’ll finish with a strong recommendation that you check out each and every videos from the Networking Field Day 13 event at [http://youtube.com/techfieldday](http://youtube.com/techfieldday). &nbsp;The presentations in my opinion really were solid and informative. &nbsp;As I see it, the product seems strong coming out of the gate and their presentation certainly reinforced that impression.

## Mea Culpa

The price of being too busy means sometimes keys, lists, sticky notes, and blog posts get lost. I was cleaning up my folders today and ran across my completed blog post on Forward Networks I wrote back in December 2016. &nbsp;So, please forgive any dated references are tenses that seem like I just saw these guys. I saw them back in November 2016.

Also reference [my general disclaimer](https://broadcaststorm.tumblr.com/2020/10/19/2016-09-11-disclaimer-for-my-blog.html) regarding my attendance - in short, no one asked me to write this, let alone what to say. GestaltIT/TechFieldDay provided travel accommodations to attend the event.

