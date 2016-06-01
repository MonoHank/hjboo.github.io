---
layout: post
title:  "DigitalOcean vs. Webfaction"
author: Ben Centra
date:   2014-09-21 12:00:00
categories: thoughts
tags: hosting digitalocean webfaction
---

![Which host to choose?]({{ site.url }}/assets/images/digitalocean-vs-webfaction.png)

I am currently debating switching web hosts from [Webfaction][webfaction] to [DigitalOcean][digitalocean]. Both hosts are marketed towards "developers," both are relatively cheap, and both have a number of services in common: one-click deploys, detailed web interfaces, online support documentation, and more. Despite the similarities, the two hosts each have their own spin on how they serve developers.

## Webfaction

![Webfaction dashboard]({{ site.url }}/assets/images/webfaction-dashboard.png)

Webfaction's goal is to reduce the amount of maintenance and setup users need to do. Updates, security patches, and general server maintenance is handled by Webfaction, so you don't need to worry about it. To make things even easier, they provide a number of one-click installers for just about any kind of web app: Django, Rails, Wordpress, Node, PHP, static HTML, and many more. They also have a one-click database setup for MySQL and PostreSQL databases, which you can manage through phpMyAdmin/phpPgAdmin. The installers and more - DNS management, bandwidth monitoring, etc - can be found in the web dashboard, which needs a facelift but gets the job done.

The trade-off with Webfaction is flexibility; you get console access to your server but not `sudo` permissions, so you're pretty much limited to what you can one-click install. I assume this is because your Webfaction server is shared, not deditcated. Though this is the only real downside, it might be a deal breaker for some.

## DigitalOcean

![DigitalOcean dashboard]({{ site.url }}/assets/images/digitalocean-dashboard.png)

While Webfaction makes deploying apps easy, DigitalOcean ocean promises the same for entire servers. They have a very simple process for creating "Droplets," their cute name for dedicated cloud servers, with various Linux distros and even some pre-configured application environments to choose from. You can also create fresh Droplets from saved images and backups of other Droplets. The one-click setup for Droplets is easy and fast; the lowest-tier Droplets are advertised to install in 55 seconds or less. Unlike Webfaction, you'll get full root access on your Droplet, so you can install whatever you want with ease. You can `ssh` in, or use the online console built into the web dashboard. 

The trade-off with DigitalOcean is management overhead. While Webfaction handles the maintenance of their shared servers, you're responsible for your own Droplet: updating packages, installing security updates, deploying new apps, etc. If this doesn't sway you, you may like the freedom DigitalOcean provides.

## Comparison

If my descriptions weren't enough, here's a quick comparison of some key features between the two hosts at the lowest/cheapest tier:

Item | [Webfaction][webfaction-features] | [DigitalOcean][digitalocean-pricing]
:---: | :---: | :---:
_Price_ | $9.50/month | $5/month
_Server Type_ | Shared | Dedicated
_CPU_ | Quad-core | Single-core
_Memory_ | 512MB | 512MB
_Storage_ | 100GB | 20GB
_SSD?_ | No | Yes
_Bandwidth_ | 600GB | 1TB
_SSH Access?_ | Yes | Yes
_Root Access?_ | No | Yes
_1-Click Install?_ | Many | Some
_Web Dashboard_ | Good | Great
_Documentation_ | [Good][webfaction-docs] | [Great][digitalocean-docs]
_Community_ | [Good][webfaction-community] | [Good][digitalocean-questions]

If you like developing locally and need a place to host your apps online with ease, I'd recommend checking out Webfaction. If you don't mind doing setup or playing sysadmin on occasion but want complete flexibility, you can get that and save a few bucks with DigitalOcean.

__UPDATE:__ I ended up going with Webfaction. I don't need the complete freedom/management overhead of DigitalOcean, and the ease of deploying and managing apps makes the extra $5 a month worth it.

[webfaction]: https://www.webfaction.com/
[digitalocean]: https://www.digitalocean.com/
[webfaction-docs]: https://docs.webfaction.com/
[digitalocean-docs]: https://www.digitalocean.com/community/
[webfaction-community]: https://community.webfaction.com/
[digitalocean-questions]: https://www.digitalocean.com/community/questions
[webfaction-features]: https://www.webfaction.com/features
[digitalocean-pricing]: https://www.digitalocean.com/pricing/ 
