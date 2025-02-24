---
title: "My Home Lab Journey"
date: 2025-02-23T17:32:50-05:00
draft: true
---

I first started creating my home lab when I learned about network attached storage devices (NAS).
I used to store all of my data on one drive were I paid monthly for around a terabyte of storage.
That worked for a while but as I started to hoard more data that wasn't an option any more.
Once I learned about NAS storage I new that was the route I was going.

So I started looking into what options I had, and in the end it came down to either building one my self or buying something like a Synology box.
My current gaming pc that I built was starting to show its age and I knew that I was going to upgrade soon.
So I decided to build a new pc and use any of my old parts for building a NAS.

For my NAS OS I installed TrueNAS Core.
I got two 6 terabyte hard drives so I could set up a mirrored raid.
Once I got the pool and all the permissions and shares set up, I started to move all my data over and canceled my Microsoft 365 subscription.

I ran my setup like that for a while.
I would turn on the box and backup my data to it, then turn it off.
Eventually though I started to do more.
I set up a VPN through the TrueNAS interface so I could access my nasbox from anywhere if it was on.

I had a GPU hooked up to it but I couldn't use it on core, or at least not without any weird tinkering.
However, I did know that a new version of TrueNAS came out a little while ago.
TrueNAS scale based on Linux instead of OpenBSD and I was much more comfortable with Linux, since at that point I was dual booting windows and Linux.
Scale also allowed for your graphics card to be isolated and passed to virtual machines.
So I made the switch.
I didn't have a plan for what I was going to do with the ability to pass my GPU to a vm, but I figured since I had it I might as well use it for something eventually.

Honestly, I didn't really end up do much with the GPU.
I did however start getting more into setting up apps through TrueNAS.
TrueNAS scale at the time used Kubernetes and there was a project called TrueCharts.

At this point I was running and experimenting some services on a VPS.
Things like Nextcloud, websites, git, etc. and I wanted to start hosting some stuff from home.
So I started trying out different things.

Some of the apps I set up were:
- Nextcloud
- prowlarr, radarr, sonarr, lidarr
- jellyfin
- syncthing
- tailscale

Most of it wasn't to difficult to setup.
There were quit a few good tutorials online and TrueCharts had good documentation with step by setup tutorials for setting up there apps.
Probably the hardest was setting up Nextcloud to be reachable outside my home network security.
Through TrueCharts this meant setting up cloudflare tunnel and revers proxy with Traefik.
Overall though this taught me a lot about what goes into setting up services for a home lab.

This setup however did have somethings I didn't like.
Mostly to do with being at the mercy of any changes TrueCharts implemented.
There were many times were things would break after an update and I would have to reinstall and go digging around the TrueCharts announcement to find out how to fix it.
Especially with Nextcloud with all the other services it depended on.

Eventually TrueNAS Scale announce that it was going to discontinue using Kubernetes for its app system and was moving over to docker.
This in turn meant this discontinue of development for TrueCharts.
With this I decided that I was going to change how I ran my services.

I knew that I wanted to just run docker separate from TrueNAS but I didn't know how I was going to go about it.
Eventually I decided to try out proxmox.
So I got a mini pc and installed proxmox on it for testing.
Following some tutorials online I got a Ubuntu server LXC containter setup running portainer.

While online through I saw that you could run TrueNAS in a virtual machine on proxmox.
I decided to try that out thinking that it would be nice to run everything on one box.

This wasn't so simple though as I had to pass my drives through to TrueNAS, which wasn't to difficult.
I also had a external drive hooked up for a backup of my TrueNAS pool.
Trying to pass through this to TrueNAS caused some problems since it was a USB device and not in my pc connected through SATA.
Then I had to pass my TrueNAS share to my separate LXC container running portainer.
This was challenging since I was running into permission issues running a unprivileged LXC container.
Then trying to pass my GPU through to docker was even more of a challenge.
This required installing the same drivers on both the proxmox host and the LXC container.
Then passing that GPU to the Jellyfin docker container for transcoding.

I did eventually get everything setup and working but if I updated proxmox or my LXC container that would break my GPU pass-through.
I would then have to reinstall my GPU drivers and restart the system again.
This could have been fixable with a script but I didn't feel like putting in the effort to set that up.
I also felt like transfer speed to my samba shares didn't feel as fast.

In the end I decided to go back to just running TrueNAS bar medal and running proxmox on the mini pc I bought.
Running TrueNAS and docker on proxmox is definitely a doable option but in the end I just didn't like it personally for my setup.

Currently I am planing to stick with the setup I have for the foreseeable future.


