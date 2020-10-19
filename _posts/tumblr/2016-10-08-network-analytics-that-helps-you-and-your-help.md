---
layout: post
title: Network Analytics That Helps You and Your Help Desk
date: '2016-10-08T14:19:08+00:00'
tags:
- NFD12
- monitoring
- network
- voyance
tumblr_url: https://broadcaststorm.tumblr.com/post/151523860913/network-analytics-that-helps-you-and-your-help
---
In my [previous post](http://bit.ly/2cv2DEx), I talked about the monitoring tools and visibility that you might typically see at a University. &nbsp;With those tools, you get a great sense of what services/hosts/devices are up/reachable. You even can extract/deduce that some degradation might be going on. &nbsp;I even talked about how the “typical” open source (or inexpensive) tools leave an important aspect of visibility out of the picture.

Visibility is an extremely powerful component of maintaining and supporting your network. &nbsp;The right data in the right hands can lead to quick and effective problem resolution.

## The Wireless Dilemma

It’s no secret that end users are increasingly mobile. &nbsp;Equally unrevealing is that troubleshooting wireless is not easy - even for seasoned network engineers. &nbsp;As mobile applications put more stress on the network, users increasingly experience connectivity issues that affect the business. &nbsp;Given the complexities involved, help desks simply resort to escalating a large number of incidents to be resolved by the (wireless) network team.

As a result, that creates a huge operational expense for large scale wireless deployments - especially at universities that are so dependent on mobility for learning spaces as well as accommodating the “residential experience” students require.

Any tool or technique that can help engineers quickly identify the root cause for a reported wireless issue provides a huge win for the university - especially as they (like many businesses) are under increasing pressure to reduce operational expenses.

## Time and Materials

Because, in my mind, the problem maintaining a wireless network isn’t an engineer training issue - it’s a time issue. &nbsp;The massive drain on time is the manual collation of information from several different sources to begin deducing the root cause: wireless controllers, authentication servers, SNMP managers, SYSLOG servers, etc. all contain various pieces of the puzzle. &nbsp;Typically, a site visit is involved by the engineer. &nbsp;Many issues have smoking guns, some do not.

For some salt in the wound, at a University, users (mostly students) typically have a low report rate for incidents. &nbsp;If they’ve come to expect that “wireless isn’t so great here”, they may live in silent suffering especially if some level of wired connection is available. &nbsp;Occasionally, though, someone may think better of it and report an issue. As a result, you are left with an incomplete picture of the scope of the problem.

Worst of all, wireless being wireless and residential spaces ripe with dirty air (electromagnetic and aromatic), you have a large potential for your network equipment being completely functional and your clients not.

## Nyansa at #NFD12

All of these pain points came to mind when I was a delegate at Networking Field Day 12 ([http://techfieldday.com/events/nfd12](http://techfieldday.com/events/nfd12)). &nbsp;The first presentation of Thursday was Nyansa ([http://www.nyansa.com](http://www.nyansa.com)) and their product Voyance.

The TL;DR of the presentation: it will tell you in simple plain English what the problem could be and possible solutions to fix it. &nbsp;And they showed an example of it on a live network (Brandeis University).

But how they manage to accomplish that is very interesting…

## How Voyance Works

The longer version: they use appliances (OVA or physical) distributed across the network - as many as needed to get a complete view of all traffic - to collect a wide range of metrics off the packet header via port traffic replication (SPAN/Monitor). Some protocol information (HTTP response times, e.g.) are also gathered. &nbsp;The packet payloads are discarded.

The crawlers (as they call them) also communicate with popular wireless controllers (Cisco, Aruba, Ruckus) to pull configuration and performance information from the wireless network. &nbsp;This integration permits Voyance to have that visibility into client wireless experience by having access to signal strength, association information (client to AP maps), and assists with mapping users to clients.

All that “performance metadata” (as I’ll call it) is sent to Nyansa’s cloud analytics engine. &nbsp;In short, the product uses analytics on all the visibility you have given it to do a few very cool things (among others):

1. Collates all those sources of information to associate them to a specific client and/or user.  
2. Recognizes commonalities between clients (e.g. 5 clients on the same AP) in order to detect common behaviors between related devices  
3. Can determine baselines for various aspects of your network automatically  

## What Voyance Can Do For Operations

When it’s all said and done, the two parts that really got me interested about the technology was automated correlation/grouping and the wireless troubleshooting. &nbsp;As I wrote in my intro, it ain’t fun and it frequently has a lot of incomplete information.

So, to see where I’m coming from, take a look at the [Nyansa Voyance Demo video](https://vimeo.com/178696262), starting at the 27:30 mark and watch for 2 minutes. &nbsp;In short - you can search for a particular user, a start and end date and get an overview of what issues that user experienced with possible causes.

<figure data-orig-width="2010" data-orig-height="1194" class="tmblr-full"><img src="https://64.media.tumblr.com/e184f6d3f2f4540d4dc61f8f0a19977c/tumblr_inline_oeqq0aXdgC1re93or_540.png" alt="image" data-orig-width="2010" data-orig-height="1194"></figure>

Just as I said in TL;DR and no I really wasn’t kidding. &nbsp;The analytics engine is making its best inference/deduction based on the data at hand. &nbsp;The best part? &nbsp;You can see the same underlying data for those determinations as well, all in one spot:

<figure data-orig-width="2034" data-orig-height="1270" class="tmblr-full"><img src="https://64.media.tumblr.com/3d83511ce8c510195a85cb3ae4e32500/tumblr_inline_oeqq2tHYyk1re93or_540.png" alt="image" data-orig-width="2034" data-orig-height="1270"></figure>

Generally speaking, there is the potential for real operational savings with this tool enabling first level support to absorb the load from the engineering teams. &nbsp;With some workflows and training, the help desk can field/resolve some of the network tickets coming in. &nbsp;Or, with insight from the Voyance analytics, have a much better chance at assigning the ticket to the correct team for resolution.

On top of that, there’s operational savings for your engineers as well. &nbsp;Not sure you are willing to wholesale trust the result from Voyance? That’s okay - you will have with all the related data available in one location related to the user/problem at hand.

The presentations also highlighted a few other features which (for brevity) I won’t discuss in depth. &nbsp;I didn’t want to leave them unmentioned though as they are also difficult operational tasks that could be assisted by Voyance.

1. Dashboard overview of your network - yes, many products have one. &nbsp;But, look at this screenshot. &nbsp;Notice the security “Global Advisories Notifications” (my edit: big red box). &nbsp;This was not covered during the #NFD12 presentation but I think your security team might be interested in the demo. &nbsp;I would have loved to have had the time to see a deeper dive of the security advisories on which it can report.

<figure data-orig-width="2198" data-orig-height="1306" class="tmblr-full"><img src="https://64.media.tumblr.com/bf17bdbaf646834fb1e2de197c05d9f6/tumblr_inline_oeqq6pP0x81re93or_540.png" alt="image" data-orig-width="2198" data-orig-height="1306"></figure>

2. Quality of Experience - how “good” was user X’s Skype call? Or, why was it so bad? &nbsp;We didn’t ask but I imagine the same questions can be asked of Avaya or Cisco telephony and/or video conferencing technologies. &nbsp;Maybe even WebEx? (pure conjecture on my part)

3. Capacity planning - it has all your performance data and trending information so this a natural outcome.

## End Game

The bulk of my experience derives from academic institutions. &nbsp;They are great, challenging, and exciting environments to work in. &nbsp;With students being the ultimate customer and bringing the latest gadgets from home, new and more complicated technologies continue to put pressure on staff.

For the last 6-8 years or so, many of us have been driving home the point that the (wired) network is a utility, like power and simply must be on at all times and rock solid. &nbsp;Now, we have the industry pushing wireless to meet those same standards as well as dropping more applications on it than ever.

So for education, I think there are several “gold stars” that make Voyance very appealing:  
1. Deep packet inspection without decrypting or storing payload  
2. No agent on client devices, no network topology changes  
3. Does not need a map or topology of your network  
4. Better guidance for help desks which at universities can be staffed by student workers a fair amount of the time.

## Bonus!

In doing a bit more leg work for this post, I ran across this link: [http://www.nyansa.com/educause/](http://www.nyansa.com/educause/)

If you are going to Educause at the end of October, Nyansa is scheduling one-on-one demonstrations of the Voyance product! &nbsp;That would be a great opportunity to check them out and really pick their brains on the features that interest you. &nbsp;The team from Nyansa at #NFD12 were very approachable and, as you can see from the videos, excited about their product.

Also, after the #NFD12 presentation, I asked about pricing - it’s based on the number of users or devices and comes in 4-5 different tiers (see [http://www.nyansa.com/faqs/](http://www.nyansa.com/faqs/) for a slightly more wordy version). &nbsp;Flat-ish pricing is very attractive to academic institutions as well.

## Disclaimer

I was a Network Field Day delegate - please be sure to review my disclaimer post: [https://broadcaststorm.tumblr.com/2020/10/19/2016-09-11-disclaimer-for-my-blog.html](http://bit.ly/2c7WIJS)

Now, if only I had a network with which to try out this product…

