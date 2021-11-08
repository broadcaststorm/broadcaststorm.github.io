---
layout: post
title: OSPF Type 3 LSA Metrics
date: '2016-02-10T00:20:24+00:00'
tags:
- ccnp
- ospf
- type 3 lsa
tumblr_url: https://broadcaststorm.tumblr.com/post/139034640543/ospf-type-3-lsa-metrics
---
As I am reviewing material for the CCNP ROUTE exam, a thought occurred to me while digging into OSPF and Type 3 LSAs: what happens to the LSA after it is initially generated? &nbsp;Namely, when an Area Border Router (ABR) generates the Type 3 LSA (Summary Network) based on a route from a non-backbone area into Area 0 (backbone area), how does it get into the other non-backbone areas?

## The Background

The explanation of how the metric is calculated (summarized from ROUTE 300-101 Official Certification Guide) - the metric of the best intra-area route (SPF determined path from Type ½ LSAs) in the remote area for the ABR to reach the subnet plus the metric of the best intra-area route in the local area this router and the ABR. &nbsp;Because of the efficiencies in OSPF and inter-area design, the first metric is included in the Type 3 LSA so the local router only has to calculate the local area’s route to the local ABR.

## The Question

But, if the local router is in area 2 and the remote subnet is in area 3, there are **TWO** ABRs in between the routers. &nbsp;So we have a Type 3 LSA for an area 3 route with an area 3 metric in it. And we have an area 2 metric to an ABR that is based on SPF. &nbsp;What about the route between ABRs? Does it matter? If so, how does the metric get discovered and included?

If you think the route (and its metric) between ABRs doesn’t matter, please go directly to jail and do not collect $200.

## The Network

This scenario was a perfect opportunity to fire up the network simulation software. &nbsp;The design for this was very simple:

<figure data-orig-width="607" data-orig-height="118" class="tmblr-full"><img src="/images/tumblr/tumblr_inline_o2bfaaybOk1re93or_540.png" alt="image" data-orig-width="607" data-orig-height="118"></figure>

The relevant configs are at the end of the post.&nbsp;

Let’s look at the LSDB and focus on the “summary net” LSAs, specifically LSID 192.168.1.1 (loopback /32 subnet from R2):

```
! Snipped and showing 192.168.1.1 LSA output here
Router3#show ip ospf database summary 

            OSPF Router with ID (3.3.3.3) (Process ID 1)

		Summary Net Link States (Area 0)

  LS age: 744
  Options: (No TOS-capability, DC, Upward)
  LS Type: Summary Links(Network)
  Link State ID: 192.168.1.1 (summary Network Number)
  Advertising Router: 1.1.1.1
  LS Seq Number: 80000001
  Checksum: 0x13AF
  Length: 28
  Network Mask: /32
	MTID: 0 	Metric: 11 
[...]
		Summary Net Link States (Area 1)

  LS age: 743
  Options: (No TOS-capability, DC, Upward)
  LS Type: Summary Links(Network)
  Link State ID: 192.168.1.1 (summary Network Number)
  Advertising Router: 3.3.3.3
  LS Seq Number: 80000001
  Checksum: 0x3B75
  Length: 28
  Network Mask: /32
	MTID: 0 	Metric: 21
```

As you can see in the last lines of each LSA block, the metric in Area 0 is “11” and in Area 1 it’s “21”. Because I set the reference bandwidth value to 10000 (10GE), the 1GE links have an OSPF cost of 10, accounting for the increase in metric between the areas.

## The Light Bulb

As with everything you learn, everything makes much more sense once you figure something out and this was no exception. &nbsp;This time around, two things I had run across suddenly became clear and sank in:

1. I built a 4 router test network to look at the 192.168.1.1 LSA on R4 and examine its metric. However, as I was gathering outputs for this post, I saw the LSA on R3 too. Why? &nbsp;Routers in the same area synchronize their LSDB as part of becoming fully adjacent neighbors. Oh. Duh. How many routers did I really need?

2. An ABR calculates the metric to a remote subnet advertised by a Type 3 LSA the same as every other router in the area: SPF-based best route to the ABR generating the LSA plus the metric listed in the LSA. In my example above, the routing table entry looks like:

    O IA 192.168.1.1 [110/21] via 10.10.30.110, 00:12:07, GigabitEthernet0/0

So, when the R3 ABR goes to generate and flood the Type 3 LSA into Area 1, it does exactly what an ABR is supposed to do: use the cost of the best route and its own router ID in the LSA, then flood it into the area.

## The Answer

Each ABR receives the Type 3 LSA like any other router. &nbsp;When it generates a new Type 3 LSA for another area, it uses its own routing information to generate the LSA metric and floods it. The “Area 0” cost to cross the backbone from one ABR to another is embedded in the generated LSAs.

## The Configs

```
! Router R2
interface Loopback0
 ip address 192.168.1.1 255.255.255.0

interface GigabitEthernet0/0
 ip address 10.10.20.120 255.255.255.0

router ospf 1
 router-id 2.2.2.2
 auto-cost reference-bandwidth 10000
 network 10.10.20.0 0.0.0.255 area 2
 network 192.168.1.0 0.0.0.255 area 2

! Router R1
interface GigabitEthernet0/0
 ip address 10.10.20.110 255.255.255.0

interface GigabitEthernet0/1
 ip address 10.10.30.110 255.255.255.0

router ospf 1
 router-id 1.1.1.1
 auto-cost reference-bandwidth 10000
 network 10.10.20.0 0.0.0.255 area 2
 network 10.10.30.0 0.0.0.255 area 0

! Router R3
interface GigabitEthernet0/0
 ip address 10.10.30.130 255.255.255.0

interface GigabitEthernet0/1
 ip address 10.10.10.130 255.255.255.0

router ospf 1
 router-id 3.3.3.3
 auto-cost reference-bandwidth 10000
 network 10.10.10.0 0.0.0.255 area 1
 network 10.10.30.0 0.0.0.255 area 0

! Router R4
interface GigabitEthernet0/0
 ip address 10.10.40.140 255.255.255.0

interface GigabitEthernet0/1
 ip address 10.10.10.140 255.255.255.0

router ospf 1
 router-id 4.4.4.4
 auto-cost reference-bandwidth 10000
 network 10.10.10.0 0.0.0.255 area 1
```

