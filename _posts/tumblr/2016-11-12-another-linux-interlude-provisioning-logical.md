---
layout: post
title: Another Linux Interlude - Provisioning logical volumes for Docker
date: '2016-11-12T15:01:16+00:00'
tags:
- linux
- docker
- lvm
- filesystem black magic
tumblr_url: https://broadcaststorm.tumblr.com/post/153092765628/another-linux-interlude-provisioning-logical
---
The context for today’s post involves extending my filesystem layout (partitioning scheme) to accommodate a large Docker repository.

If you’ve read my previous post about my partitioning philosophy here ([https://broadcaststorm.tumblr.com/2020/10/19/2016-11-12-a-brief-linux-interlude-partitions-logical.html](https://broadcaststorm.tumblr.com/2020/10/19/2016-11-12-a-brief-linux-interlude-partitions-logical.html)), you’ll know that I do things a bit different from the default installation layout.

Short version, my Fedora 24 uses the following filesystem layout:

- /dev/sda1 : /boot (1GB)  
- /dev/sda2 : LVM partition for hostVG (rest of space)  
- rootLV : / (10GB)  
- varLV : /var (10GB)  
- tmpLV : /tmp (5GB)  
- homeLV : /home (20GB)  

Since I’m looking to add space to support Docker images, I need to be worried about the **/var/lib/docker** directory tree. For one container image and instance, 10GB might be fine. &nbsp;As I indicate in [that other blog](https://broadcaststorm.tumblr.com/2020/10/19/2016-11-12-a-brief-linux-interlude-partitions-logical.html) post though, running out of space in /var isn’t good. &nbsp;Let’s go ahead and add a new logical volume (LV) so that we protect the rest of /var and can keep an eye on Docker space consumption. &nbsp;

**Note** : these instructions assume that you have not installed Docker yet.

## Nothing up my sleeve

The general steps to create this new mount point are:

1. Create LV - **lvcreate -L 10G -n dockerlv rootvg**  
2. Format filesystem (XFS) - **mkfs.xfs /dev/rootvg/dockerlv**  
3. Update FSTAB with this line -&nbsp; **/dev/rootvg/dockerlv /var/lib/docker xfs defaults 0 0**
4. Create /var/lib/docker - **mkdir -p /var/lib/docker**  
5. Mount the LV - **mount /var/lib/docker**  

At this point, you have&nbsp; **/var/lib/docker&nbsp;** with 10GB of its own space live on your system. &nbsp;However, there are a couple of important finishing touches that lots of folks forget:

- Change owner, group, and permission settings to match use case.  
- Restore the SELinux contexts for that directory tree.  

The new filesystem on the new LV has a fresh directory tree, the top-level of which defaults to root for the UID and GID with permissions of 0755/drwxr-xr-x. &nbsp;In this case, root UID and GID is correct but the directory permissions should 0700 (chmod 0700 /var/lib/docker) - only the root user should be able to read/write and access the directory.

A new filesystem defaults to having an **unlabeled\_t** &nbsp;type context. The end result - everything created underneath that top-level will also be **unlabeled\_t**. &nbsp;Fortunately, SELinux enabled systems have an easy fix for this: **restorecon -vR /var/lib/docker**

## Secret filesystem mojo

Let’s assume that you previously had a running Docker environment with a couple GB of images and container instances. &nbsp;You are getting tight on space and want to create a separate LV to permit for growth.

You stop Docker. &nbsp;You follow the steps above, as is. &nbsp;You start Docker. &nbsp;Docker reports a brand new, clean environment with no images, instances, or volumes.

Where did your existing images, containers, and volumes go? &nbsp; **No where.** &nbsp;In my partitioning scheme above, they still live on the varLV logical volume - in a sub-directory of the top-level lib directory.

In UNIX/Linux, mounting a partition or logical volume performs some redirection magic (WAY beyond scope of this post) so that /var/lib/docker points to the new LV mount point. &nbsp;However, the /var filesystem still has the underlying filesystem structure that points to the original data.

To see this easily (with my partition scheme above), on a freshly booted system, console in as the root user (not via sudo). &nbsp;Unmount the /home filesystem, create a temporary file, and remount the /home filesystem. &nbsp;Unmount again and watch your temporary file re-appear.

## /var/lib/docker wrapup

I freely admit to not fully grasping the mechanics of Docker where the rubber meets the road (that is, where the pretty powerpoint presentations are converted to files and directories on disk).

That being said, I can discern the existence of black magic when I see the following:

> `# du -hs /var/lib/docker2.4G	/var/lib/docker# ls -lh /var/lib/docker/devicemapper/devicemappertotal 1.4G-rw-------. 1 root root 100G Nov 12 10:33 data-rw-------. 1 root root 2.0G Nov 12 10:33 metadata`

For those with poor eyesight, a 2.4G directory tree has a 100GB file in it. &nbsp;Why that is I will one day investigate… but not today.

Because, the relevance here is this: A typical method of mounting the new LV in a temporary location, syncing the data, and remounting the new LV in its final location does not work.

You will not easily be able to migrate the environment without some significant work and solid understanding of the inner ways of Docker. &nbsp;From what I’ve read online, that appears to change every 6 months too :)

I strongly suggest simply blowing away your old Docker environment (docker rm and docker rmi all containers and images), stopping Docker, and deleting the contents of **/var/lib/docker** before mounting the new LV based **/var/lib/docker**.

