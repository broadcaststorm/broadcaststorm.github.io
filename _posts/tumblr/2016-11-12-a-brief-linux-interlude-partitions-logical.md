---
layout: post
title: A Brief Linux Interlude - Partitions, Logical Volumes, and Layouts
date: '2016-11-12T14:42:32+00:00'
tags:
- linux
- lvm
- fhs
tumblr_url: https://broadcaststorm.tumblr.com/post/153092095993/a-brief-linux-interlude-partitions-logical
---
In this day of dynamic applications, rapid development cycles, and throw away “OS instances” - be it VMs cloned from golden images or containers - often we are so focused on the forest we fail to realize the various parts of the ecosystem that all play a part.

One particular issue I had today brought to mind some aspects of provisioning I haven’t thought about in years, instinctively following a general pattern that has served me well.

## Filesystem Layout

For those who mainly consume Linux as an end user, you may not be aware of something called the **Filesystem Hierarchy Standard (FHS)** which defines the directory structure for a “Unix-like” Linux distributions. &nbsp;You can read a bit about it on [Wikipedia](https://en.wikipedia.org/wiki/Filesystem_Hierarchy_Standard)&nbsp;([https://en.wikipedia.org/wiki/Filesystem\_Hierarchy\_Standard](https://en.wikipedia.org/wiki/Filesystem_Hierarchy_Standard)). &nbsp;It originally started as a Linux community effort, is maintained by the Linux foundation, but can be used by non-Linux operating systems as well.

The short of it: the directory tree you see in a Linux distribution isn’t an accident or haphazard. &nbsp;There are specific directory paths for specific types/classes of data. &nbsp;Most major distributions conform to the standard - which is a good thing - as it permits developers to lay out software installations in predictable ways.

The standard also permits the OS to provide some default behaviors (such as SELinux contexts) that can be leveraged to help secure the system a bit better.

## Disk Partitioning

A hierarchy is fine and must exist for the user and developer but… the system administrator who installs the operating system has the freedom to map that filesystem onto the underlying storage device(s) in any number of ways.

The first level - disk partitioning - has gotten very simple over the years. &nbsp;Linux systems rarely need more than 2 basic disk partitions on the primary disk: one for the /boot directory and one for the logical volume management subsystem. &nbsp;The separate /boot partition is required because the boot loader is fairly minimal, supporting only a few (sometimes just one!) filesystems.

The second partition is the LVM partition from which you build logical volumes to support all the other directory mount points for your operating system. For a primer on LVM, see Wikipedia ([https://en.wikipedia.org/wiki/Logical\_volume\_management](https://en.wikipedia.org/wiki/Logical_volume_management)) or Fedora ([https://docs.fedoraproject.org/en-US/Fedora/14/html/Storage\_Administration\_Guide/ch-lvm.html](https://docs.fedoraproject.org/en-US/Fedora/14/html/Storage_Administration_Guide/ch-lvm.html)).

## Logical Volumes/Mount Points

When you choose the default filesystem layout in a Fedora installation, you get that two physical partition approach for for /boot and LVM as described above. &nbsp;Additionally, Fedora creates two logical volumes:

1. one for the root filesystem (/)  
2. one for your swap space.  

With laptops and personal workstations, that’s a decent enough approach. It gives an end user as much of the physical disk for whatever personal activities they want.

If you’ve built servers for a living, you quickly start twitching at all the potential pitfalls that ultimately result in filling up your root partition - which, if you didn’t know, is bad (see&nbsp;[https://youtu.be/wyKQe\_i9yyo?t=47s](https://youtu.be/wyKQe_i9yyo?t=47s)):

- /home - user data can grow until all space is consumed  
- /var/log : system logs that, even with log rotation/compression, grow over time (especially that new application which failed to put logrotate.d rules in place)  
- /var/cache : application cache data (software updates, e.g.) that never seem to get clean up over the life of the installation  
- /var/lib : application state data  
- /tmp - temporary files  

Under this default layout,&nbsp; **Murphy’s Law&nbsp;** presents itself in any number of ways, such as:

- Logs got full 1-2 weeks ago so the debug messages are lost for the issue you are experiencing with (your web app you are building|the VM you are kickstarting from your laptop|etc.)  
- You’re downloading software that takes an hour or two so you go to bed to wake up to an incomplete transfer  
- You do commit/push your code into your laptop/workstation repository only to run out of space mid-commit. &nbsp;Ouch.  

## My preference

To avoid those calamities with the default filesystem layout, I’ve built my own layout - none of which is terribly original as most of the structure follows guidelines from days long gone by. &nbsp;Given the twitches I outlined above, you might have guessed that the layout looks something like ( **no matter how big the system drive is** ):

- (physical partition) /boot : 1GB  
- / : 10 GB  
- /var : 10 GB  
- /tmp : 5 GB  
- /home : 20 GB  

It’s only recently (last year) that I had to bump /boot from 512MB to 1GB because of the increase in consumption (under Fedora) caused by the intersection of multiple kernels and systemd. &nbsp;Also, /var went from 5GB to 10GB because the yum/dnf/PackageKit cache data has started to consume a large quantity of space.

So, I have done pretty well with the layout. &nbsp;The best part about that starting point is that those logical volumes are not static in size - they can be grown at any time as your needs require.

## Room to Grow Intelligently

And note: I stated those sizes are regardless of the drive’s size. &nbsp;I have a standard practice of leaving the rest of the system disk unallocated. &nbsp;Doing so helps you in many obvious ways:

- space usage discipline : namely, when you run out of space in /home, you can evaluate whether or not to clean up the usage or grow it based on actual need.  
- emergency reserve of space  
- flexibility to expand only those directory mount points (logical volumes) that really need extra space  
- ability to create new mount points when, for example, you want to create an isolated mount point for website content (/var/www/html) or a PostgreSQL database (/var/lib/pgsql).  

## Summary

So, many times - especially for “throw away” installs - you don’t really care how your filesystem is deployed. &nbsp;However, for longer living systems, such as a home workstation or Linux VM on a Mac or a server, a little bit of preparation can save you some headache later down the road.

Check out my next post where I pull in the Docker angle to this refresher.

