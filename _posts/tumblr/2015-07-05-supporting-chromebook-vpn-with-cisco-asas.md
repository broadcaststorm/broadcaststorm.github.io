---
layout: post
title: Supporting ChromeBook VPN with Cisco ASAs
date: '2015-07-05T15:37:36+00:00'
tags:
- ASA
- VPN
- L2TP over IPsec
- ChromeBook
tumblr_url: https://broadcaststorm.tumblr.com/post/123303057993/supporting-chromebook-vpn-with-cisco-asas
---
The ChromeBook is an interesting piece of technology that works extremely well (IMHO) in organizations which are medium to heavy adopters of Google Apps. &nbsp;The integration of the apps within the device is darn near seamless and working offline is darn near transparent.

However… &nbsp;if you need to access institutional resources while offsite and require the use of a VPN, your VPN options are darn near limiting (IMHO): OpenVPN or L2TP over IPsec. &nbsp;ChromeOS currently does not permit 3rd part VPN clients (yet) so using vendor supplied software (such as Cisco AnyConnect) is not available either. &nbsp;And, by my estimation, most enterprises have a commercial VPN concentrator so, in essence, you are practically limited to L2TP over IPsec.

Since I work on Cisco ASA appliances, that is good news as L2TP over IPsec is a supported VPN protocol set. &nbsp;Unfortunately, the various different documents out there seem to have all the bits and pieces needed to configure it correctly, just not all in the right place. &nbsp;There’s some reasonably good documentation available from Cisco for generic&nbsp;[L2TP over IPsec setup](http://bit.ly/1H0zGsq)&nbsp;(for v9.1) and for [Android clients](http://bit.ly/1fejwoP). &nbsp;Also, Google chimed in with a good page on VPN setup for the&nbsp;[ChromeBook itself](http://bit.ly/1fekGk3)&nbsp;but inaccurate information on configuring the [ASA itself](http://bit.ly/1LMvBxy).

## The Critical Lessons Learned

1. If you are using L2TP over IPsec with pre-shared keys (PSK), you **must** &nbsp;use the DefaultRAGroup tunnel-group in the ASA. &nbsp;If you want/have to use a custom named tunnel-group, you must use user certificates if my read of the documentation is correct.&nbsp;
2. On the ChromeBook itself, you must **never, ever, ever** put any string into the **Group Name** &nbsp;setting. &nbsp;You can’t even use “DefaultRAGroup”, which is the tunnel-group used on the ASA. &nbsp;
3. On the ChromeBook itself, if you accidentally specified a **Group Name** &nbsp;setting, we could not find a way to clear it! &nbsp;If you left the field blank to try and delete it, the client used the cached data you supplied previously! The only solution was to delete the connection profile on the ChromeBook and start over.
4. (minor) The IKEv1 transform set name only has local significance and does not have to match the various documents. &nbsp;You merely have to make sure it has the **esp-3des** and **esp-sha-hmac** &nbsp;components and it is set to **mode transport**. &nbsp;Our ASA have the transform set defined out-of-the-box under a different name but it was not applied to the dynamic crypto map.
5. The example below specifies the use of “LOCAL” AAA. Since we use an external RADIUS server, you have to ensure that the server is configured to support the authentication types configured in the&nbsp; **ppp-attributes** &nbsp;for DefaultRAGroup.
6. The default number of simultaneous VPN logins for the default group policy is **LOW** &nbsp;(3). &nbsp;The example below includes a commented out command to raise it to 25.
7. We found we had to force the TCPMSS size lower as the ChromeBook did not calculate the correct MTU size for the tunnel interface, particularly to 1300 ( **sysopt connection tcpmss 1300** ). &nbsp;See last section on **Active Code Development** &nbsp;below for more details on this one.

This next item is not a gotcha per se, but something I learned about the CLI in working on this project. &nbsp;The **crypto dynamic-map** &nbsp;command is additive, rather than an assignment. &nbsp;By this, I mean that it will append the specified&nbsp;transform-set to the currently defined entries rather than reset the list to be only the specified **transform-set**. &nbsp;This is contrary to the behavior one would suspect or fear given the behavior of the&nbsp; **switchport trunk allowed vlan** &nbsp;command in IOS. &nbsp;To remove a single **transform-set** , you simply **no crypto dynamic-map** &nbsp;the individual **transform-set**.

## The Minimal ASA configuration

```
!  Create required security association (SA) for L2TP over IPsec on ChromeBooks
crypto ipsec ikev1 transform-set ESP-3DES-SHA-TRANS esp-3des esp-sha-hmac 
crypto ipsec ikev1 transform-set ESP-3DES-SHA-TRANS mode transport


!  Add transform set to dynamic map
crypto dynamic-map SYSTEM_DEFAULT_CRYPTO_MAP 65535 set ikev1 transform-set ESP-3DES-SHA-TRANS 
crypto map vpn_map 65535 ipsec-isakmp dynamic SYSTEM_DEFAULT_CRYPTO_MAP


!  Apply crypto maps to vpn-outside interface
crypto map vpn_map interface vpn-outside
crypto ikev1 enable vpn-outside


!  Note:  Priority 120 is the default on ASA5505.  You can re-order the policies so that it is 10.  
crypto ikev1 policy 120
 authentication pre-share
 encryption 3des
 hash sha
 group 2
 lifetime 86400

!  Set up L2TP over IPsec group policy for DefaultRAGroup (use your DNS info)
group-policy DefaultRAGroupPolicy internal
group-policy DefaultRAGroupPolicy attributes
 vpn-tunnel-protocol l2tp-ipsec 
 dns-server value W.X.Y.Z
 default-domain value example.com
 ! vpn-simultaneous-logins 25

!  Dynamic IP address pool for VPN profiles
ip local pool ChromeBook 192.168.1.1-192.168.1.49 mask 255.255.255.0

!  Associate address pool and group policy to tunnel-group
tunnel-group DefaultRAGroup general-attributes
 address-pool ChromeBook
 default-group-policy DefaultRAGroupPolicy
 ! This example relies on local account.  Set up below for other AAA options
 ! authentication-server-group YOUR_AAA_AUTHN_SERVER
 ! accounting-server-group YOUR_AAA_ACCT_SERVER

!  Add Pre-shared Key to tunnel-group
tunnel-group DefaultRAGroup ipsec-attributes
 ikev1 pre-shared-key CHANGE_THIS_PSK_FOR_YOUR_VPN


!  Disable PAP, enable MS-CHAPv2 on tunnel-group
tunnel-group DefaultRAGroup ppp-attributes
 no authentication pap
 authentication ms-chap-v2

!  Lower the TCPMSS so that the (potentially incorrect) ChromeBook MTU calculation doesn't affect traffic flow 
sysopt connection tcpmss 1300
```

Unlike the documentation links provided above, my example chooses to define an explicit **group-policy** for the DefaultRAGroup rather than rely on DfltGrpPolicy **&nbsp;** that is assumed by DefaultRAGroup (by default, of course). &nbsp;As a matter of principle, we try to keep the various default groups that all policies and tunnel groups fall back to clean. &nbsp;With the group policy, that was possible. We really did not like having to rely on DefaultRAGroup **tunnel-group** &nbsp;but also did not have the infrastructure for managing user certificates at this time.

Also, for those concerned (like us) about pre-shared-key entries in the DefaultRAGroup, we checked with TAC: the **ipsec-attributes&nbsp;** are not inherited by tunnel groups that inherit DefaultRAGroup.

## Active Code Development

While we were testing and debugging the configuration, Google made several code changes related to VPN. &nbsp;Our ChromeBooks started on the stable branch, version 41.0.2272.102. &nbsp;This version required the TCPMSS setting listed in item 7 above. &nbsp;However, in the development branch (at the time), several releases came out that at first fixed the MTU issue (42.0.2302.4) and then broke L2TP VPN altogether (42.0.2305.2). &nbsp;Fortunately, a colleague was following the releases like a hawk, convinced Google there was a problem, when it happened, and Google issued a fix (42.0.2311.153,&nbsp;43.0.2357.52).

So, in short, you may not need the TCPMSS setting change. &nbsp;You will know very quickly if you do. &nbsp;Your VPN connection will get established just fine. However, your experience will present as your typical MTU mismatch problem: some websites and traffic types work, some do not. &nbsp;In this case though, you need to check regular websites and not Google’s websites - Google’s sites work with an ASA TCPMSS value of 1380. &nbsp;Others, such as CNN, Fox News, or your own company’s sites most likely will not.

## Updates

(2015-07-09) Forgot to mention - the ASA version these instructions were built against was 9.1(5).

