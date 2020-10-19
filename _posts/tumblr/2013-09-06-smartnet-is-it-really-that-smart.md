---
layout: post
title: SmartNet...  Is it really that "Smart"?
date: '2013-09-06T22:57:16+00:00'
tags:
- SmartNet
- Cisco Partners
tumblr_url: https://broadcaststorm.tumblr.com/post/60506294236/smartnet-is-it-really-that-smart
---
Hardware failure repairs. Firmware updates. Software updates. Facts of life for the IT professional.

As are the support contracts that provide those services. In some ways, they provide restful sleep at night. In others, nothing but inexperience induced frustration.

As a system administrator, the support life was fairly easy: buy a server with 3 or 4 or even 5 years of hardware maintenance up front. Every piece of hardware had a unique identifier (e.g. serial number) that was all you needed to get the service you wanted.

Prior to assuming my new network engineer role, I had a very narrow view of SmartNet (the Cisco variety of maintenance and software support) - namely, two MDS 9140 SAN switches. One died in the middle of the night and I called TAC (Cisco’s support service). It mostly worked as expected - once I gave them the correct serial number, I had a replacement switch within 4 hours.

As a network engineer, the view into SmartNet maintenance – and here I refer to managing the SmartNet itself not the quality of the actual hardware/software maintenance – got much deeper and far darker.

First, there is rarely a default hardware warranty of any real duration on Cisco - wireless access points and some 48-port lower end switches excepted. On UCS servers or Nexus switches, you pay for annual support on day one. Given the price, not ideal but okay fine.

Second, the purchase of SmartNet gives you a contract number. Okay, same for out-of-warranty hardware.

Now for the fun part… the contract number is tied to the Cisco Partner that you purchased it from. Not necessarily the end of the world as it is similar with IBM (how many customer numbers do you have?). Except, you can’t open a TAC case unless the contract number is associated with your Cisco login. You can spend a lot of money each year, have 24x7x2hr support, AND be in a production down situation… And not open a case… [1].

And, your Cisco partner is key to associating the contract number to your login. I’ve seen this work one of two ways. One, you give them your login name and they work with Cisco to get the contract number associated to your login. Or, they have to give you the contract number and you make the request directly from Cisco.

Given this crucial aspect of accessing the support you’ve paid for, you’d think the Cisco Partner would have this perfected. Sadly, no. And, it seems that even after missing it on the first contract creation… It still gets missed on subsequent contract creations.

That is the first REAL deviation from my IBM server days - all you need is the machine type and serial number to open a support case. Regardless, I might add, if that server was under warranty or on a post-warranty support contract.

So, in short, you have to be extremely proactive in getting SmartNet contract numbers when they are created and ensuring they are associated with you and your team. Unfortunately, something that should just be automatic is far from it.

And if you think that’s all the “Smart” in SmartNet, just wait until the next post.

[1] That being said, most everyone I’ve ever dealt with at Cisco has bent over backwards on all levels when I declare a “production down” issue. So, there are delays but, in the few cases I’ve had, it hasn’t stopped work on a case until business hours paperwork is done,

