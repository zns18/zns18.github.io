---
layout: post
title: Hosting solutions
date: 2015-04-25
comments: true
archive: false
tags: blog, web development
blog: true
description: This is a useful catalog of all sorts of hosting solutions - physical, virtual and homegrown.
---

If you are planning on making a website, the first decision you need to
make is how to host your website. Do you use a VPS? Do you use a full
metal server? Do you learn NGINX and Apache and do it yourself? What
about scaling? What about storage? What about the cloud? *There are lots
of quetions to answer and I’ll try to answer them*.

Also I made a very informative table of some current good web hosting
solutions.

Basics
======

**A web host** allows your web application to be accessible to the world
wide web. This typically involves a server being somewhere that will 
receive requests and send back the information to the client. How do you
find such a server?

There are lots of companies that will sell you a server or a piece of a
server to host your web page. There are a couple of different types.

Full computer solutions
-----------------------

Full computer solutions are servers that are blank slates in the sense
that they are just computers running Linux or similar. For these you’ll
need to configure the server software (NGINX or Apache). This is easy to
do. These are full computer solutions, which can be nice if you want to
take advantage of other computer programs or system tools (like CRON).

-   **Physical Servers**: These are physical computers that a company
    will sell you. The nice thing about these is that they are housed in
    the company’s building so you don’t have to worry about electricity
    and heat issues and you also get full access to a real computer.
-   **Virtual private servers**: These are the same as physical servers,
    except that the computer that you access is contained within a
    *virtual environment*. Usually this doesn’t matter, but can
    sometimes mean that you have to share resources with somebody.
-   **Homegrown server**: This is the same as above too, except that you
    have to buy it, put it in your house, forward the ports and pay for
    the electricity.

Per diem services
-----------------

If you don’t want to setup any server stuff, and you want a more
plug-and-play type server, there are per-diem services out there. These
are like OpenShift and Amazon EC2 which allow you to pay (sometimes
free) to access there server. All you need to do is “deploy” your code.

Hosting Solutions
=================
<div class="row">
<div class="span12">
<div class="table-responsive">
<style type="text/css">
.table{
    width:500px;
    border: 1px solid #ccc;
}
.table td{
    border: 1px solid #ccc;
}
.id, .date, .action{
    width:1px;
}
.date{
    white-space: nowrap;
}
</style>
<table class="table-bordered table-striped" >
<thead>
<tr>
<th align="left">Website</th>
<th align="left">Cost</th>
<th align="left">Pros</th>
<th align="left">Limitations</th>
<th align="left">Geography</th>
</tr>
</thead>
<tbody>
<tr>
<td align="left"><a href="https://www.scaleway.com/pricing/">Scaleway</a></td>
<td align="left">euros10/month</td>
<td align="left">Private server</td>
<td align="left">4 core ARM processors, 50GB disk, 2GB memory</td>
<td align="left">Europe</td>
</tr>
<tr>
<td align="left"><a href="http://www.kimsufi.com/us/en/">Kimsufi</a></td>
<td align="left">$7/month</td>
<td align="left">Private server</td>
<td align="left">1 core 1.8 Ghz processor, 500GB disk, 2GB memory</td>
<td align="left">Worldwide</td>
</tr>
<tr>
<td align="left"><a href="https://www.atlantic.net/cloud-hosting/pricing/">Atlantic.net</a></td>
<td align="left">$5/month</td>
<td align="left">Virtual private server</td>
<td align="left">1 cpu, 20GB Disk, 512MB memory, 2TB bandwidth</td>
<td align="left">United States</td>
</tr>
<tr>
<td align="left"><a href="http://cloudatcost.com/">CloudAtCost</a></td>
<td align="left">$1/month</td>
<td align="left">Virtual private server</td>
<td align="left">10GB Disk, 512MB memory, 500GB bandwidth</td>
<td align="left">Canada</td>
</tr>
<tr>
<td align="left"><a href="https://www.digitalocean.com/pricing/">Digital Ocean</a></td>
<td align="left">$5/month</td>
<td align="left">Virtual private server</td>
<td align="left">20GB Disk, 512MB memory, 1TB bandwidth (<a href="https://education.github.com/pack">$100 credit for new users</a>)</td>
<td align="left">Worldwide</td>
</tr>
<tr>
<td align="left"><a href="https://cloud.google.com/compute/#pricing">Google</a></td>
<td align="left">~$5/month</td>
<td align="left">Virtual private server</td>
<td align="left">1 shared core, 10GB disk, 600MB memory</td>
<td align="left">Worldwide</td>
</tr>
<tr>
<td align="left"><a href="https://express.ikoula.com/en/public-cloud">Ikoula</a></td>
<td align="left">euros9/month</td>
<td align="left">Virtual private server</td>
<td align="left">0.5 core, 50GB disk, 512MB memory, unlimited bandwidth</td>
<td align="left">France</td>
</tr>
<tr>
<td align="left"><a href="https://koding.com/Pricing">Koding</a></td>
<td align="left">Free</td>
<td align="left">Virtual private server</td>
<td align="left">1 core, 3GB disk, 1GB memory</td>
<td align="left">United States</td>
</tr>
<tr>
<td align="left"><a href="https://www.linode.com/pricing">Linode</a></td>
<td align="left">$10/month</td>
<td align="left">Virtual private server</td>
<td align="left">1 core, 24GB disk, 1GB memory, 2TB bandwidth</td>
<td align="left">Worldwide</td>
</tr>
<tr>
<td align="left"><a href="https://www.online.net/en/dedicated-server/dedibox-scg2">Online.net</a></td>
<td align="left">euros6/month</td>
<td align="left">Virtual private server</td>
<td align="left">1 core 1.6 Ghz, 500GB disk, 2GB memory, unmetered bandwidth</td>
<td align="left">France</td>
</tr>
<tr>
<td align="left"><a href="https://uberspace.de/prices">Uberspace</a></td>
<td align="left">Pay what you want, at least 1 euro</td>
<td align="left">Virtual private server</td>
<td align="left">10GB disk, 100GB bandwidth, shared hosting</td>
<td align="left">Germany</td>
</tr>
<tr>
<td align="left"><a href="https://www.vultr.com/pricing/">Vultr</a></td>
<td align="left">$5/month</td>
<td align="left">Virtual private server</td>
<td align="left">1 core, 15GB disk, 768MB memory, 1TB bandwidth (<a href="https://www.vultr.com/freetrial/">Free trial</a>)</td>
<td align="left">Worldwide</td>
</tr>
<tr>
<td align="left"><a href="https://www.ramnode.com/vps.php">Ramnode</a></td>
<td align="left">$5/month</td>
<td align="left">Virtual private server</td>
<td align="left">1 core, 10GB disk, 512MB memory, 1TB bandwidth</td>
<td align="left">United States</td>
</tr>
<tr>
<td align="left"><a href="https://www.pythonanywhere.com/pricing/">Python Anywhere</a></td>
<td align="left">$5/month</td>
<td align="left">Specifically tailored to Python</td>
<td align="left">3,000 CPU-seconds/day 2GB disk, "Low" bandwidth</td>
<td align="left">United States and UK</td>
</tr>
<tr>
<td align="left"><a href="https://www.openshift.com/products/pricing/plan-comparison">OpenShift</a></td>
<td align="left">Free</td>
<td align="left">3 gears (3 applications, or 1 application scaled up), lots of "cartridges"</td>
<td align="left">1CPU, 1GB Disk, 512MB memory, <a href="https://developers.openshift.com/en/managing-scaling.html#how-scaling-works">16 connections per gear</a>, <a href="https://help.openshift.com/hc/en-us/articles/202301674-Are-there-any-data-transfer-limits-imposed-on-OpenShift-Online-applications-">No data transfer limits</a></td>
<td align="left">United States and Europe</td>
</tr>
<tr>
<td align="left"><a href="https://aws.amazon.com/free/faqs/">Amazon EC2</a></td>
<td align="left">Free for one year</td>
<td align="left">Similar to Openshift for deploying</td>
<td align="left">1GB memory, 750 hours usage, free expires after 12 months, 15 GB bandwidth</td>
<td align="left">Worldwide</td>
</tr>
<tr>
<td align="left">Homegrown</td>
<td align="left">onetime $30 (Raspberry Pi) - $600 (nice dedicated computer)</td>
<td align="left">Full access, simplicity, plenty of storage/memory/processor speed</td>
<td align="left">Home bandwidth limitations (typically low upload speeds)</td>
<td align="left">Your home</td>
</tr>
<tr>
<td align="left"><a href="https://www.webfaction.com/features">WebFaction</a></td>
<td align="left">$8.50/month</td>
<td align="left">Rock-solid, good tech support + deployment tools, Quad-core 3.4Ghz, 100GB disk</td>
<td align="left">Shared hosts, 600GB bandwidth, 512MB application memory</td>
<td align="left">Worldwide</td>
</tr>
<tr>
<td align="left"><a href="https://www.heroku.com/pricing">Heroku</a></td>
<td align="left">Free</td>
<td align="left">Similar to Openshift for deploying</td>
<td align="left">512MB memory, shared hosting, 1 worker</td>
<td align="left">Europe and United States</td>
</tr>
<tr>
<td align="left"><a href="https://run.pivotal.io/pricing/">Pivotal Web Services</a></td>
<td align="left">$2.70/month</td>
<td align="left">Deployment with <code>cf push</code></td>
<td align="left">1 app with 128MB memory</td>
<td align="left">United States</td>
</tr>
</tbody>
</table>
</div></div>
</div>