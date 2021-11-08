---
layout: post
title: Static and BGP routes - Some Cisco IOS Routing Table Fundamentals
date: '2016-04-11T11:02:19+00:00'
tags:
- bgp
- static routes
- professional development
- simulations
tumblr_url: https://broadcaststorm.tumblr.com/post/142632266988/static-and-bgp-routes-some-cisco-ios-routing
---
In our last episode on fundamentals, I revealed that a site-wide power outage brought down our primary network closet and, despite the secondary network closet being fully operational, we were no longer able to reach the Internet. This post will dig into how that happened. Sadly, it was not a misconfigured server that was to blame (see t-shirt [http://amzn.to/1VCzidN](http://amzn.to/1VCzidN)).

## The Network

Enough time has passed such that the detailed notes of the incident have sadly gone missing. Tragic because a lot of good log entries were captured and would have been extremely useful for all you folks as well.

When all you have are lemons, make lemonade - I took the opportunity to get my simulation environment cleaned up, build out a BGP template network, and clone it with the details below. The design replicates the topology in place during the outage enough to demonstrate the fundamentals here today.

<figure data-orig-width="674" data-orig-height="476" class="tmblr-full"><img src="/images/tumblr/tumblr_inline_o5h4onuEV91re93or_540.png" alt="image" data-orig-width="674" data-orig-height="476"></figure>

Most of the high level design goals were “the usual” - when R1 goes down, R2 services entire traffic load of the enterprise and vice-versa. Proper traffic re-routing when individual links go down as well. &nbsp;ISP1 simply advertises a default route to us and to ISP2 (which is then advertised to us as a backup link).

One important goal that comes into play today was that all incoming traffic to the enterprise flow through R1 even if the Gi0/0 interface to ISP1 went down. &nbsp;Therefore, Internet traffic coming from ISP2 into R2 (when ISP1 fails) should be routed to R1 before sending into the enterprise - assuming R1 is up, of course.

The relevant configurations of the switches are here:

```
! R1
router bgp 65001
 bgp router-id 172.30.0.1
 bgp log-neighbor-changes
 network 172.30.0.0
 neighbor 192.168.11.1 remote-as 65004
 neighbor 192.168.21.2 remote-as 65001
 neighbor 192.168.21.2 next-hop-self
!
ip route 172.30.0.0 255.255.0.0 Null0
!
! R2
router bgp 65001
 bgp router-id 172.30.0.2
 bgp log-neighbor-changes
 network 172.30.0.0
 neighbor 192.168.21.1 remote-as 65001
 neighbor 192.168.21.1 next-hop-self
 neighbor 192.168.22.1 remote-as 65005
!
ip route 172.30.0.0 255.255.0.0 192.168.21.1
!
! R4
router bgp 65004
 bgp router-id 172.10.1.1
 bgp log-neighbor-changes
 network 0.0.0.0
 network 172.10.0.0
 neighbor 192.168.11.2 remote-as 65001
 neighbor 192.168.11.2 prefix-list BGP_PERMIT_DEFAULT_ONLY out
 neighbor 192.168.12.2 remote-as 65005
 neighbor 192.168.12.2 prefix-list BGP_PERMIT_DEFAULT_ONLY out
!
ip prefix-list BGP_PERMIT_DEFAULT_ONLY seq 10 permit 0.0.0.0/0
ip route 0.0.0.0 0.0.0.0 Null0
ip route 172.10.0.0 0.0.0.0 Null0
!
! R5
router bgp 65005
 bgp router-id 172.20.1.1
 bgp log-neighbor-changes
 network 172.20.0.0
 neighbor 192.168.12.1 remote-as 65004
 neighbor 192.168.22.2 remote-as 65001
!
ip route 172.20.0.0 255.255.0.0 Null0
!
```

## Lesson #1 - Static routes need a reachable next-hop

After the power was restored and chaos subsided, some investigation of the syslog entries on the secondary gateway revealed the static route for our network address space was deleted from the routing tables.

From our simulation network, you can see this in the console output with “debug ip routing” and “debug ip bgp updates” enabled on R2:

```
*Apr  5 03:14:20.115: RT: del 192.168.21.0 via 0.0.0.0, connected metric [0/0]
*Apr  5 03:14:20.115: RT: delete subnet route to 192.168.21.0/30
*Apr  5 03:14:20.116: RT: del 192.168.21.2 via 0.0.0.0, connected metric [0/0]
*Apr  5 03:14:20.116: RT: delete subnet route to 192.168.21.2/32
*Apr  5 03:14:20.120: RT: del 172.30.0.0 via 192.168.21.1, static metric [1/0]
*Apr  5 03:14:20.120: RT: delete subnet route to 172.30.0.0/16
*Apr  5 03:14:20.126: BGP(0): route 172.30.0.0/16 downd
```

Because the interface to R1 went down, the connected subnet and route to the next-hop was removed from the routing table. &nbsp;As such, without a next-hop, the static route was removed as well.

Despite a few years under my belt on data center environments, this was a big surprise to me back then - with my DC using Catalyst 6500s using EIGRP to core and static routes to downstream firewalls, the next-hop never went away because the connected subnet was a Vlan interface.

## Lesson #2 - BGP requires routes it originates to be in the routing table

The seasoned BGP folks out there have probably seen this moment coming since the R2 configs were shown. Without that 172.30.0.0/16 static route loaded on R2, BGP had nothing to advertise to the ISP2 router. Links are up. BGP peer state is Established. No Google to search for answers.

This was probably the most fascinating aspect to BGP that I learned - it’s not the Internet that disappears, it’s your enterprise that withdrew from the Internet. It is your responsibility to announce to the Internet that you are prefix A with ASN B and can be reached at next-hop C.

From R2, you can communicate to your ISP2 BGP neighbor without issue, giving a false sense of security in this situation.

## Lesson #3 - Simulation environments are great but real hardware is better.

Okay, this lesson was learned while prepping the blog and not during the post-event root cause analysis. Even an event from years ago can provide learning opportunities so (word of advice time) always be looking for opportunities to learn, to lab up an scenario and tinker with it (in a LAB!!!). &nbsp;

But, at the same time, as in normal troubleshooting, ensure what you are seeing makes sense! &nbsp;Take for example the following two issues I ran across:

1. When a peer router interface goes down, the local interface that connects to it does not. &nbsp;Specifically, when you shutdown Gi0/1 on R1, Gi0/1 on R2 stays up/up. Now, eventually BGP times out and the BGP peer is torn down. As a result, you need to carefully consider whether you need to manually bring down local interfaces to simulate failures.
2. Even with shutdown on both R1 and R2 Gi0/1 interfaces, the iBGP peer status did not immediately come down despite the neighbor IP address belonging to the connected subnet. I would really love some real hardware to determine if this is the true behavior. I don’t recall this happening on the Catalyst 6500s (multi-layer switches)… but it’s been long enough I would prefer to verify which case is true.

Bringing it back to the simulation now … In the “normal operating environment”, R1 advertises 172.30.0.0/16 via BGP:

- Using eBGP to send it to R4
- Using iBGP to send it to R2

As a result, for the prefix 172.30.0.0/16, R2 has a static route with Administrative Distance of 1 and an iBGP route with AD of 200. Under normal running, the AD=1 static route is loaded into the routing table.

Once the static route is withdrawn though, the AD=200 iBGP route is now eligible to be loaded into the routing table:

```
*Apr  5 03:14:20.127: BGP(0): Revise route installing 1 of 1 routes for 172.30.0.0/16 -> 192.168.21.1(global) to main IP table
*Apr  5 03:14:20.127: RT: updating bgp 172.30.0.0/16 (0x0)  :
    via 192.168.21.1   0 1048577
*Apr  5 03:14:20.127: RT: add 172.30.0.0/16 via 192.168.21.1, bgp metric [200/0]
```

Except there are two problems with that -

1. With the iBGP peer relationship not being torn down, the prefix from iBGP stayed in the BGP table until the hold timer expired. &nbsp;It eventually expired and the route disappeared. As I said before, I would like some real hardware to verify if that is correct behavior.
2. BGP is not supposed to use a route to a prefix if the next-hop is not reachable (remember N WLLA OMNI?). That next-hop is definitely not reachable. It clearly indicated the interface down and the route removed. Something in the simulation is not quite right.

So, in short, while simulations help with prototyping a scenario, you still must use your brain to make sure what you are seeing makes sense.

## Lesson #4 - Professional development is key to you and to your employer

Your stable network is great for the business, but that doesn’t necessarily translate to you knowing what you need to do your job. This was my first real introduction to network operations back in the day. While we all learned many things about operations and our perimeter routing configuration, I personally learned a very important lesson - I was smart enough to figure out how to fix a broken thing that was previously working.

However, I was a good many lessons short on being able to drive new solutions. Obviously, from today’s post, routing was at the top of the list. To ensure I had some quick basics, I spent a couple days reading CCNP ROUTE 642-902 sections on core routing and BGP.

Even if your employer doesn’t support (financially) your professional development, you absolutely have to continue to pursue expanding your knowledge base. Whether it is fighting hard to get certified or simply reading the certification guides - do something! Have a plan - first for what will benefit your current employer and role, and second for your future responsibilities.

Don’t know what you want to do 3-5 years down the road? Don’t worry. As you broaden and deepen your current understanding, I’ve found the road map presents itself.

## The Solution to Our Perimeter Issue

So, what is the solution? We looked at two options:

1. On R2, set the next-hop on the static route for 172.30.0.0/16 to be Null0.
2. On R2, we could leave the existing configuration and simply add another static route for 172.30.0.0/16 with AD=201 and next-hop of Null0. &nbsp;When the AD=1 static route is withdrawn because the interface is down, the AD=201 route will be loaded. &nbsp;

Both options solve the primary issue of the 172.30.0.0/16 prefix always being in the routing table. However, we do have to consider additional changes in this simulation to meet the design requirements that (a) all traffic flow through R1 before entering the Enterprise and (b) when R1 is down, R2 should forward traffic directly to R3.

Fortunately, in our real production environment, we already had additional components in the design (not shown here) that handled the traffic flow so that option 1 was all that was needed.

## Teaser

I just licensed VIRL ([http://virl.cisco.com/](http://virl.cisco.com/)) so I am going to get that set up and see if the official Cisco product has any of these discovered deficiencies. &nbsp;I will let you know.

