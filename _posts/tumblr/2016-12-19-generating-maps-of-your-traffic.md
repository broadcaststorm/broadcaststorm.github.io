---
layout: post
title: Generating Maps of Your Traffic
date: '2016-12-19T05:57:12+00:00'
tags:
- SolarWinds
- NFD13
- Path Discovery
tumblr_url: https://broadcaststorm.tumblr.com/post/154672812148/generating-maps-of-your-traffic
---
Any network engineer is familiar with ‘traceroute’ - a network tracing tool that leverages ICMP or UDP to report the hop by hop path your (traceroute) traffic takes from your client to the specified destination address. &nbsp;In short - it send packets to the destination address with the TTL initially set to 1 and each successive sent packet has a larger TTL value. Each router attempting to route a packet whose TTL has reached zero drops that packet and returns an ICMP Time Exceeded message. &nbsp;Thus, each router in the path reports to you its existence.

Obviously, as you are trying to potentially troubleshoot connectivity between two hosts, this information can be helpful in determining where traffic is getting dropped or taking a right turn at Albuquerque instead of a left turn.

Except, many things can go wrong or provide an incomplete picture:

- ICMP messages can be blocked by routers along  
- The path presented is the current singular path taken. If equal cost multipathing is in play, you may not see the other paths your traffic can take. &nbsp;Or worse, some of the probes take one path and other probes take another path.  
- If policy based routing is in play along your path, you will not see those various policy paths with simple traceroute.  

And, of course, that path information from traceroute is “real time” - unless you have cobbled together your own script and database, you aren’t going to have historical information for those paths.

In the past few months, I’ve run across presentations and demos on a few different ways you can get a better idea of the various paths available and their changes over time. &nbsp;This latter aspect playing an important role in troubleshooting potential issues on your network.

## Those who don’t know history are destined to repeat it.

The first enhancement to the traceroute approach I’ve encountered was NetBeez - a monitoring solution that I wrote about back in September ([http://bit.ly/2cv2DEx](http://bit.ly/2cv2DEx)). The approach is straight forward and campus LAN focused - using virtual or Raspberry Pi (or similar hardware) agents across the network, centrally schedule traceroute tasks from those agents to defined targets and store the results.

I was able to deploy a NetBeez at my home office and quickly understood some of the anomalous performance hits of my office internet connection, simply by having a historical record of all the traceroute data. &nbsp;Most of the time, the path information was complete because that traffic is not impeded by routers along the destination.

Similar story would be true for a campus network environment where you wouldn’t block locally originated/destined ICMP traffic. The NetBeez approach is a great, simple solution for straightforward network designs and paths.

But what happens when you have to traverse the Internet with all of its various providers, peering relations, and (seemingly) ad-hoc policies toward ICMP? &nbsp;You need a better approach to understand the complexity of paths possible both in the forward and reverse direction.

## Once You Start Down the Multiple Paths…

Enter SolarWinds - which initially previewed a network path discovery tool, something they labelled a “lab toy”, called NetPath back in February 2016 (Tech Field Day 10). &nbsp;A month ago, (Networking Field Day 13), Chris O'Brien returned to present the latest updates in the context of now being an official feature available through the Network Performance Monitor product SolarWinds offers.

Chris points out that they quickly found that simple ICMP probing was insufficient for the task at hand - so SolarWinds developed some proprietary techniques for crafting packets to help discover multiple paths to the destination. With a couple patent pendings under their belt, Chris was able to more freely discuss how they conduct their probing to build out the path information. &nbsp;The best part: it was a white board discussion that you’ll definitely want to check out here ([http://bit.ly/2hvSZaQ](http://bit.ly/2hvSZaQ)). &nbsp;There is a wealth of wisdom in the mechanics of tracing your path that Chris relates in their experience developing the technology.

NetPath takes the simple traceroute approach with historical context to the next level by providing that multiple path information. The NetPath data is gathered by a SolarWinds provided Windows-based agent that you install in your network, at the various points where you would like to source the discovery (similar to the NetBeez approach). The probe data is stored in a SolarWinds SQL database for the historical trending/evolution of your traffic path.

The only location for that data is your local, on-site instance of SolarWinds. &nbsp;And, visually displayed, the path looks something like this:

<figure class="tmblr-full" data-orig-height="558" data-orig-width="2736"><img src="https://64.media.tumblr.com/79946b04e040d25075904a84df1c5af1/tumblr_inline_oifhu2Kp6m1re93or_540.png" data-orig-height="558" data-orig-width="2736"></figure>

SolarWinds doesn’t stop there - path discovery is a neat tool but doesn’t do you much good in a vacuum. &nbsp;The SolarWinds product suite, however, isn’t in a vacuum - between NCM (network configuration manager), NPM (network performance manager), and NTA (network traffic analyzer), SolarWinds has a very rich understanding of your network and can leverage those components to help determine potential root causes for changes in the path behavior. &nbsp;The simplest example of this integration: correlating configuration changes with path behavior changes - namely, a redundant link disappears from your path because you shutdown the interface.

Coming from a strong Linux background, SolarWinds hasn’t been one of my favorite tools - for a few technical reasons - but the power of correlating/integrating the components is fairly apparent. &nbsp;Quite frankly, the ease of use for these NetPath related features was pretty compelling as well - something I can’t say for other products in the SolarWinds suite.

## The Point of No Return?

We’ve been conditioned for decades by our local networks to think that level of visibility is sufficient for understanding our traffic flows. To some extent, at the local level, that would be true. &nbsp;However, when we expand that approach to the Internet - traffic flows are a lot more complicated. &nbsp;Asymmetric routing is a way of life because the route availability and policies for each transit AS can force the return traffic along a different path.

Currently, SolarWinds doesn’t do the reverse path - “because it’s really hard” - but with that explanation, I feel like they are working on it even if they are not saying so or committing to it. &nbsp;There are other companies, such as ThousandEyes, that are discovering the reverse path so it is just a matter of research and development.

## Wrapping it up

While humorously calling it “mean time to innocence”, a few products I have seen recently are really advancing the state of monitoring and problem detection. &nbsp;Network outages and hardware breaks have always been the low hanging fruit for these tools for quite some time. &nbsp;With overlays, SD-WAN, and PBR, networks have long since had complexities that make troubleshooting subtle but user impacting performance issues a manual and low effort for engineers.

It’s encouraging to me from a “bread and butter” campus network background to see tools that are focusing quickly on isolating these issues - especially as network complexities, such cloud adoption and transitions from MPLS to more SD-WAN based services, are unavoidable in even the most traditional networks going forward.

