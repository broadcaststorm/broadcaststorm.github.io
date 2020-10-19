---
layout: post
title: Cisco Nexus 1110 Appliance Overview
date: '2015-04-08T15:48:53+00:00'
tags:
- nexus
- cloud services platform
- nexus1110
- concepts
tumblr_url: https://broadcaststorm.tumblr.com/post/115874309178/cisco-nexus-1110-appliance-overview
---
Today we are going to discuss one possible method for deploying the Cisco Nexus 1000V Virtual Supervisor Module (VSM) software appliance. &nbsp;If you aren’t familiar with the Nexus 1000V or its components, you should take a look at the official [product data sheet](http://www.cisco.com/c/en/us/products/collateral/switches/nexus-1000v-switch-vmware-vsphere/data_sheet_c78-492971.html)&nbsp;or my previous post on its [conceptual operation](http://t.co/9rhhjqG4kD)&nbsp;(or both!)

Since the VSM is a software appliance, we need an underlying platform on which to run it. &nbsp;You have two options available: &nbsp;a virtualization environment such as VMware or Hyper-V; or, the Cisco Nexus 1110 appliance. &nbsp;Both approaches will have its merits and one will fit better in your environment than the other. &nbsp;The less comfortable your network engineers are with VMware, the more likely you will prefer the Nexus 1110 appliance. &nbsp;If you simply can’t get the access needed within VMware, you’ll prefer the Nexus 1110 appliance. &nbsp;And so on. &nbsp;But, I’m not here to pitch products. &nbsp;My goal is to get you up to speed.

**Components and Functions**

First and foremost, let’s get some terminology of the appliances out of the way.

**Nexus 1110 Appliance** &nbsp;[[http://bit.ly/1CGuOfi](http://bit.ly/1CGuOfi)]

This is the physical sheet metal you get to touch. &nbsp;Like many of Cisco’s physical appliances (think ISE’s PAN, MNT, and PSN appliances), it is really a Cisco UCS C-series server. &nbsp;Unfortunately, if you’ve been around this product long enough, you have to suffer through a marketing name change for this appliance.&nbsp;

1. The NX-OS 4.x code train referred to this appliance as the&nbsp;“ **Virtual Services Appliance** ” (VSA). &nbsp;  
2. NX-OS 5.x refers to the appliance as the&nbsp;“ **Cloud Services Platform** ” (CSP). Don’t be fooled, though. &nbsp;  

It has components generally not found on a network device:

1. **Cisco Integrated Management Controller** (CIMC) [[http://bit.ly/1MSEo02](http://bit.ly/1MSEo02)]&nbsp;- an out of band interface to remotely manage the server: namely, monitoring (temperature, power, fan speeds, hard drive failures) and console access (both KVM for the server **AND** serial console to the CSP appliance.  
2. Keyboard, Video, and Mouse (KVM) - yes, just like your desktop except the appliance fits nicely into a rack. &nbsp;This method of access is initially used to configure the CIMC and then rarely used afterwards in my experience.  

There are two models of the Nexus 1110 appliance: &nbsp;the 1110-S model with 6x1GE copper interfaces and the 1110-X model with 2x10GE SFP+ interfaces. This post focuses on the 1110-S model.

**Nexus 1110 Cloud Services Platform software**

The firmware that enables the appliance to support multiple virtual switch functions in a high availability (HA) model [using two CSP appliances, of course]. It is really just a Linux operating system (yes, more aspects of “servers” invading the network space) whose CLI has been modified to (sort of) resemble NX-OS. You don’t need to learn Linux to use it, don’t panic!

**Nexus 1000V Virtual Supervisor Module (VSM) Open Virtualization Archive**

An&nbsp;“OVA” is merely a packaging format for software (or “virtualized”) appliances. So, it’s simply a file but, in this case, you need the one specific for the Nexus 1010 and 1110 appliances. &nbsp;Because, as if it wasn’t confusing enough yet, you have different VSM software appliance formats (the VMware specific ones are not relevant to these posts):

1. OVA for Nexus 1010/1110  
2. OVA for VMware  
3. ISO installer for VM created in VMware  

This OVA is a file that you copy to the CSP appliance in order to deploy the VSM into a&nbsp;“virtual service blade”

**Virtual Service Blade (VSB)**

A virtual service blade is merely Cisco’s way of abstracting virtualization concepts and operations from the network engineer so that all he/she must do is “install a supervisor blade” into the CSP to deploy a new “switch” or service blade (NAM, etc). &nbsp;The number of VSBs that you are allowed to “install” into the CSP depends on the model. &nbsp;

**Networks in the Nexus 1110 CSP Environment**

The CSP appliances come in pairs so that you can have an active-standby HA environment. &nbsp;Similar to the Nexus 1000V VSM, the CSP has management and control VLANs. &nbsp;Originally (in the 4.2 VSA&nbsp;“days”), these management and control VLANs were shared by the appliance and all the VSBs. &nbsp;That is no longer (necessarily) the case. &nbsp;You can separate the management and control VLANs if you wish. &nbsp;So, despite adding a lot of terminology, I will preface each VLAN with the environment to which it belongs:

1. CSP Management VLAN: This VLAN is used to permit you to connect to the management interface of the CSP appliances in order to manage the CSP.  
2. CSP Management Interface: The interface in the CSP software that is created, assigned the Management IP address for the appliance, and used to receive connections via SSH.
3. CSP Control VLAN: Used by the CSP physical appliances to establish the HA relationship and pass heartbeats.
4. CSP Control Interface: Currently, the CSP only supports a Layer 2 based control plane (see references for more info). &nbsp;As such, this interface is created in the CSP software and communicates at layer 2 only to discover the other CSP appliance in the HA pair.

There is a&nbsp;“data” VLAN that is referenced in all the documentation for the CSP. There is very little information in the deployment guides for the CSP about the data VLAN other than it is used by service blade appliances such as the NAM. It is generally outside the scope of a basic CSP deployment to configure the data VLAN. &nbsp;I have not yet had the pleasure of using it. &nbsp;I’ll post about it later once I do.

**Wrap Up**

This post is all about concepts and setting the foundation for the cloud services platform. &nbsp;Next up, let’s start down the road of getting them powered up and configured. &nbsp;A sneak peek to the process:

1. Rack and stack the appliances
2. Connecting the keyboard, video, and mouse to the appliance
3. Connecting the server management (CIMC) adapter network adapter
4. Power the device and configure the CIMC
5. Update CIMC and server firmwares
6. Update Nexus 1110 CSP firmware
7. Cable up CSP network ports and configure upstream ports
8. Configure Nexus 1110 CSP firmware
9. Verify CSP HA operations
