---
layout: post
title: SmartNet Encore
date: '2013-11-13T22:41:44+00:00'
tags: []
tumblr_url: https://broadcaststorm.tumblr.com/post/66935887071/smartnet-encore
---
A close friend of mine reminded me of one last frustration with SmartNet and I didn’t want to forget to post it. So, forgive this one last SmartNet post in order for me to ensure the record is more complete.

As I’ve mentioned, parts break. Service contracts are in place to replace broken parts. As with any vendor, upon identifying a failed component, an “return merchandise authorization” (RMA) is generated. You get the number. They ship a part. You replace it and ship back the broken part. Network functions.

All good, right? As you might suspect from the existence of this post, no it’s not all good.

You now have a part and serial number on your network that you did not buy nor have registered against a SmartNet contract. … What?!?!

No, Cisco does not update its databases based on the part sent to you and the part you shipped back. I’ve been told that Cisco delegates that task to the Cisco Partner from whom you purchased the SmartNet contract. Okay fine. Someone is supposed to update it. But…

How does the Partner know the part has been replaced? You don’t call them for support. You call Cisco. To the best of my knowledge, Cisco doesn’t inform the Partner either. Otherwise, it wouldn’t be a problem.

That’s right. You have to tell them. The part. The old serial number. The RMA number. The new serial number.

Since the SmartNet contract is tied to a Cisco Partner, it would be trivial to generate a daily, weekly, or monthly report of serial numbers replaced and the RMAs that could be sent to the Partner.

A simple RMA receiving process (which surely must already exist) can tie the RMA to a return part and serial number. Since the RMA is tied to a SmartNet contract, that data could be sent to the Partner too.

But alas, no. You are required to do everything except actually make the contract update.

Now, in practice, most parts are not directly tied to a SmartNet contract… Their parent chassis or switch device is. So, maintaining 100% accuracy typically does not happen and the effort does not seem to be justified.

Cisco TAC to their great credit and dedication to customer support has yet to deny service for a 48-port line card because it wasn’t in their database.

But, surely, this mess could be improved.

