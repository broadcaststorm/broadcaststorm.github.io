---
layout: post
title: Windows or Linux - Why does it have to matter?
date: '2013-06-07T00:31:37+00:00'
tags:
- linux
- network engineer
- systems engineer
- visio
tumblr_url: https://broadcaststorm.tumblr.com/post/52357487104/windows-or-linux-why-does-it-have-to-matter
---
A fairly long post today that focuses mainly on the Systems to Network engineer transition. Might be interesting to those networkers who are curious about a day in the life of ‘those systems guys’.

As a Linux systems engineer, over time, you become very adept at eating, drinking, and even breathing through the text based command line terminal. With very powerful utilities such as ‘sed’, ‘awk’, ‘perl’, ‘grep’, and native capabilities of your shell (such as ‘bash’), you can take any structured output from one command and produce a very simple list of data (e.g. convert a webserver log file into multiple lines of just IPs and URLs requested).

With Cisco IOS, you maintain access to the command line for the most part. However, the range of utilities and output manipulations are extremely limited. Today, with IOS 12.x (and all its variants), you only get the equivalent of ‘grep’. Some of that is starting to change with IOS 15.x as it bears more of a Linux flavor. NX-OS, derived from SAN-OS and Fibre Channel, is exposing more of its Linux underpinnings as well.

Challenge number one with performing more networking engineering tasks: the amount of cutting and pasting of CLI output from the network device into a text editor. With the lack of built-in tools on the switch, necessity requires the use of OS utilities on that output or the “before and after” sets of output.

Honestly, it hasn’t been difficult but it has seemed extraordinarily cumbersome. Once the output is in a text file, the regular UNIX utilities are available to work their magic. It is annoying (but understandable) that the command history is not maintained across sessions. And, with all that pasting into the OS, a certain diligence in purging temporary files or storing the output in meaningful directory structures is important for maintaining an uncluttered user environment.

Challenge two: operating system. Unfortunately, I am the odd man out on the network team. Everyone else lives in a Windows world (as does the rest of the community it seems). While dealing with that reality comes with a Linux lifestyle (and I’m definitely not complaining), a few unique hurdles presents itself:

a. Connection profiles for the entire network. The team maintains the profiles (hostnames and IPs) for all the network devices in the configuration files for the software SecureCRT. Other than a spreadsheet, the network management system, and network monitoring system, that information doesn’t exist anywhere else. As you can see, tough formats for a Linux system to consume.

To date, I’ve not yet tried to convert these profiles to a friendly Linux format. The past few years I’ve been getting by with IP addresses that I’ve remembered or successfully guessed based on established addressing patterns. That’s going to have to change soon.

b. Windows file shares. Everything shared on the team is through Windows file shares. Not really an insurmountable hurdle, per se. This issue mainly required a few hours of information gathering and service configuration, as well as dusting off some old memories of CIFS and Samba.

c. Java. The best thing since sliced bread. Write once, run everywhere. Remember the utopia? Unfortunately, not all network device management involves a command line. Security devices, while possible to do via CLI, really seems to be better managed using the Java based ASDM. Same story for optical transport and Cisco UCS.

The open source Java implementations for Linux simply do not work or work reliably for these applications. Hacking out those implementations from the native Linux distribution and installing the Oracle variant is not trivial but, once accomplished, seems to do the trick for newer versions of these applications, despite the lack of explicit support for Java on Linux.

d. MS Visio. As a network architect, I become the owner and maintainer for most of the core network design drawings. Standard for the industry, these drawings are all built in MS Visio. Unfortunately, while Linux has made up substantial ground in “productivity software” (e.g. Libre Office), no one has really made any tool in Linux that quite matches the power and flexibility of Visio, nor one that will import/export Visio files.

Over the years, I have used various technologies to overcome “that one Windows application” issue (Virtual Box running Windows XP, CodeWeavers CrossOver Office Windows emulator, dual booting). Today, to maintain some level of work throughput without a lot of time spent dealing with OS issues, I’m simply using an old Windows desktop to get by.

One other alternative is to live 100% in a Windows environment and install the CygWin suite of utilities that will provide much of the UNIX utilities I know and love. Definitely an option but one that requires some time and effort. For the stubborn Linux systems engineer, though, this option feels more like surrender.

