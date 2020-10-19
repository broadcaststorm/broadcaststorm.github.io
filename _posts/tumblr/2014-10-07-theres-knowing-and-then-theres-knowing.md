---
layout: post
title: There's knowing... And then there's knowing.
date: '2014-10-07T22:38:42+00:00'
tags: []
tumblr_url: https://broadcaststorm.tumblr.com/post/99454559028/theres-knowing-and-then-theres-knowing
---
The summary of the message this evening is that we system administrators are very much spoiled by our Operating System providers (Red Hat, in my case).

It amazes me that, every time I think I really understand a subject, I am humbled by the almighty protocols of the network. In my defense, some of these instances are because configuration or operation of the technology has changed in ways that don’t make sense.

An example of each is in order and both involve Cisco ASA. Configuration of static and dynamic NATs is fundamentally different in general compared to the FWSM but that wasn’t the difficult part. ASAs not only have three stages for NAT ordering (manual, auto, after-auto manual) … Okay, get that and think I can envision a couple different use cases for that. But, the curve ball: the order within each stage matters. So, even if you define a specific 1:1 static NAT for a particular host, if that NAT statement occurs after a valid dynamic NAT rule that matches, your traffic will generate a dynamic NAT. Not anywhere near intuitive like matching the most specific route in a routing table. And, to boot: unless you specify a line number, like access list entries, your new NAT is appended to the end. Oops.

Second, the management interface on the ASA appliance. Nexus and UCS platforms have management ports with dedicated IP addresses. However, you can’t say ‘just like the ASA’. You see, despite the label both on the appliance and on the CLI of ‘management’, there is a very important subtle difference between Nexus and ASA: the Nexus systems employ a VRF to effectively isolate the interface from the rest of the switching layer (by default). So, for us server guys, it’s an out of band management interface and no way to leak into the switching layer or vice versa.

Not so on the ASA: no VRF in that appliance. It is simply another interface just like all the others to deliver an IP packet to be evaluated and routed out another interface. What?!?! Because I want my outside traffic to route into my ultra secure management network? (Yes, yes. I know. ACLs. Still.)

Which brings me back to my initial point: OSes and software packages come delivered with stock configuration files, typically with locked down setups and standard features configured properly. From there, you simply add a few customizations and you are off and running. Not so with networking. While there are copious amounts of configuration and reference guides out there, you have to understand the best practices, know all the configuration options, and oh yeah make the design changes to fit your scenario.

I get a great deal of friendly grief on the job for all the books on the shelf. But, despite some of the learning curves above, my design work and deep diving on the details resulted in three successful FWSM-to-ASA upgrades with a total of 4 weeks of planning and preparation for all three. Did I mention we had to relocate all the access switch uplinks as well? So, yeah, I read a lot and build crazy detailed Visio files… In the end, I’m good with that.

And I no longer think “how hard could that be”…

