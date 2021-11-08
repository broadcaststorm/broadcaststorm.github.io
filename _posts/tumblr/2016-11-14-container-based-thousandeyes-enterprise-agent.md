---
layout: post
title: Container-based ThousandEyes Enterprise Agent Install on Fedora 24
date: '2016-11-14T13:33:29+00:00'
tags: []
tumblr_url: https://broadcaststorm.tumblr.com/post/153179834198/container-based-thousandeyes-enterprise-agent
---
I had the wonderful opportunity to be a Networking Field Day delegate back in August ([http://techfieldday.com/event/nfd12/](http://techfieldday.com/event/nfd12/)) where we saw the latest developments from ThousandEyes. &nbsp;If you don’t know anything about them, you should really check out their presentations ([http://techfieldday.com/appearance/thousandeyes-presents-at-networking-field-day-12/](http://techfieldday.com/appearance/thousandeyes-presents-at-networking-field-day-12/)) which include demonstrations of their product. The technology was impressive enough that one of the delegates was begging them to take his money!

Their instructions on setting up the Docker based agent are really straightforward as most container deployments tend to be. Here’s a partial screen shot of how to get to the downloads:

<figure data-orig-width="2510" data-orig-height="894" class="tmblr-full"><img src="/images/tumblr/tumblr_inline_ogn9ljANqJ1re93or_540.png" alt="image" data-orig-width="2510" data-orig-height="894"></figure><figure data-orig-width="1632" data-orig-height="486" class="tmblr-full"><img src="/images/tumblr/tumblr_inline_ogn9lySzBh1re93or_540.png" alt="image" data-orig-width="1632" data-orig-height="486"></figure>

While I’ve been following Docker a great deal and have used it a little bit in lab environments, I’ve never deployed a container that had persistent storage (volumes) - let alone 3! Not to worry - the ThousandEyes portal provides a nice form to walk you through the process:

<figure data-orig-width="1866" data-orig-height="1298" class="tmblr-full"><img src="/images/tumblr/tumblr_inline_ogn9mtsQIV1re93or_540.png" alt="image" data-orig-width="1866" data-orig-height="1298"></figure>

Simply plug in the container name, Docker version, and volume parent directory. With that information, they provide the exact Docker commands to pull the image and instantiate the container instance as shown - they embolden the dynamic information you provide so you can also visually see how that information is specified to Docker.

But, before I can do that, I must point out something that may affect you but definitely affects me…

## Filesystem and Logical Volume Interlude

With Docker, you need to have space for the image repository, container instances, and persistent volumes. &nbsp;On Fedora, this data defaults to the FHS suggested location of **/var/lib/docker/volumes**. &nbsp;If you have the default filesystem layout from a standard Fedora install, you might not have to worry (provided you have space!).

However, as you might have read in a previous post ([https://tmblr.co/ZdckEt2Ea—v](https://tmblr.co/ZdckEt2Ea---v)), I use a different layout. &nbsp;Check this second post ([https://tmblr.co/ZdckEt2Eb2ZUy](https://tmblr.co/ZdckEt2Eb2ZUy)) to read more about how I prepared my filesystem for the Docker images.

Back to our regularly scheduled program…

## Houston, We Have a Problem

All the commands worked just fine - except the enterprise agent did not appear in the portal. &nbsp;Docker reported that the container kept restarting. Running the **docker logs blog-container** showed:

```bash
*** Running /etc/myinit.d/60-copy-dnsrootkey.sh...
cp: cannot stat '/var/lib/te-agent/dns-rootserver.key': Permission denied
```

**/var/lib/te-agent&nbsp;** is mapped to one of the persistent data volumes for the container. &nbsp;First thought - crap, what did I do wrong:

- Double checked syntax for docker run.  
- Verified volume directories were created in /var/lib/docker/volumes/  
- Permissions looked good  
- Look at /var/log/messages - proceed to swear at systemd conspiracy  
- Look at journalctl output.  

And this pops out of the noise:

```bash
Nov 08 09:58:24 minbari.docker.local audit[4310]: AVC avc:  denied  { write } for  pid=4310 comm="cp" name="te-agent" dev="dm-0" ino=9224657 scontext=system_u:system_r:svirt_lxc_net_t:s0:c182,c898 tcontext=system_u:object_r:usr_t:s0 tclass=dir permissive=0`
```

Yes, our old friend SELinux - except, I followed operating system standard procedures to ensure **/var/lib/docker** reset all of its SELinux contexts to the proper values.

## Docker and SELinux

Some extra details are in order - As mentioned, I’m using a fully updated Fedora 24 VM (date 2016-11-10) running the latest kernel at the time (4.8.6-201.fc24.x86\_64). &nbsp;I am using the Docker packages that are provided by the Fedora Project. This means v1.10.3 (current available is v1.13). I haven’t had time to test this cleanly but, from my research, the Docker latest repo does not change the story.

A quick Google search found this post from Project Atomic - [http://www.projectatomic.io/blog/2015/06/using-volumes-with-docker-can-cause-problems-with-selinux/](http://www.projectatomic.io/blog/2015/06/using-volumes-with-docker-can-cause-problems-with-selinux/)

Essentially, when the container is using SELinux enforcement internally, the SELinux context that Docker is running with (svirt\_lxc\_net\_t) must be able to read/write to the volume where it is mounted within the container. &nbsp;Generally speaking, svirt\_lxc\_net\_t does not have permissions to do that. &nbsp;So, the internal container must reset the SELinux permissions on the volume’s mount point.

Why do we use SELinux again? **Because it’s the right thing to do ™**.

Unfortunately, even though we followed the FHS and reset contexts, Red Hat SELinux policy hasn’t been adapted for this issue. It might be because, as stated in that ProjectAtomic post, Docker accepted a patch way back in 1.7 to resolve this.

The solution is to append the ’-v’ volume argument with ’-z’ or ’-Z’.&nbsp;The final ‘docker run’ command (based on the screenshot above) is:

```bash
docker run \
  --hostname='blog-container' \
  --memory=2g \
  --memory-swap=2g \
  --detach=true \
  --tty=true \
  --shm-size=512M \
  -e TEAGENT_ACCOUNT_TOKEN=21zpxbjzsrvknlcgkcoxp4w04ung2261 \
  -e TEAGENT_INET=4 \
  -v '/var/lib/docker/volumes/thousandeyes/blog-container/te-agent':/var/lib/te-agent:Z \
  -v '/var/lib/docker/volumes/thousandeyes/blog-container/te-browserbot':/var/lib/te-browserbot:Z \
  -v '/var/lib/docker/volumes/thousandeyes/blog-container/log/':/var/log/agent:Z \
  --cap-add=NET_ADMIN \
  --cap-add=SYS_ADMIN \
  --name 'blog-container' \
  --restart=unless-stopped \
  thousandeyes/enterprise-agent /sbin/my_init
```

## Thousand Eyes and Support

Remember? This post was about the ThousandEyes Enterprise Agent container install. &nbsp;Well, I just want to give a shout out to the ThousandEyes support chat - which I engaged half way through this effort. &nbsp;I merely clicked on the little “cartoon bubble” chat icon and stated my problem.

Luis engaged the chat and worked with me on it for about 30 minutes. While chatting, he downloaded the Fedora 24 installer and went to work on replicating the issue - which, of course, he couldn’t. &nbsp;My luck, eh? &nbsp;I had to leave the live chat but he opened a support case with the chat history.

A little discovery yielded the fact that he was cheating - his Docker process was running unconfined. &nbsp;I don’t have detailed information but I imagine he is running Docker “native” (latest from Docker’s repo) and not the Fedora version.

But, for someone who had left the trial period on his account, that was a level of support I was not expecting. &nbsp;Kudos ThousandEyes!

## Final wrap

The ’:z’ and ’:Z’ options both resolved my problem and my container is up and running without issue. I’ve set up some tests between the enterprise and cloud agents. The container is generating data and I’m looking forward to seeing how it works.

