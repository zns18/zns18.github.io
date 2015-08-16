---
layout: post
title: Dealing with lots of traffic using load balancing
date: 2015-06-15
comments: true
archive: false
tags: blog, web development, something, else
blog: true
description: My experience with beginning web development and how to deal with high impact websites using load balancing.
---

Introduction
============

Its often very useful to submit ideas and new projects to the forums and
sites for peers to review and give important criticism. I often have
done that with projects on this blog, for instance to [the Raspberry Pi
forums](https://www.raspberrypi.org/forums/), or Reddit. When I first
posted my [Music discovery app](http://www.musicsuggestions.ninja/) to a
very popular forum, the [/r/InternetIsBeautiful
group](https://www.reddit.com/r/InternetIsBeautiful/comments/3455li/type_any_band_name_and_get_a_link_that_plays/),
I didn’t realize exactly what would happen and how easily it would shut
down my website through immense popularity that ensued - a phenomenon
known as a “*Hug of Death*”.

<div class="col-sm-12">
    <p align="center">
<img src="{{ "/assets/images/hug_attack.png" | prepend: site.url }}" alt="Image (C) 2014 Zakeena. Licensed under CC-BY." class="img-rounded img-responsive">
    </p>
</div>

Hugs of Death have been analyzed
[here](https://nsone.net/2014/10/reddit-sine/) but I was totally unaware
of them when I posted it. Had I known, I would have done some more
thorough testing of the capabilities of my web app and planned
accordingly (*more on that later*). Here’s is my analysis of the Hug of
Death I incurred, and some notes on how to avoid them in the future.

Analysis of the Hug of Death
============================

I posted my web app to the [/r/InternetIsBeautiful
group](https://www.reddit.com/r/InternetIsBeautiful/comments/3455li/type_any_band_name_and_get_a_link_that_plays/)
in the morning of April 28th, 2015. My web app consisted of a Python
program that did some light calculations and some HTTP requests and was
handled by Gunicorn WSGI workers. I was hosting the server on a small
virtual machine (2 processors, 2GB memory) and I was using NGINX as a
reverse proxy, and I had 3 Gunicorn workers going.

My NGINX logs have all the requests and errors since my sites inception,
so I converted the timestamps to minutes since the submission of my site
and plotted the number of requests per minute as well as the number of
errors per minute (from the NGINX error log file). From this data I was
able to ascertain the true beginning of the *Hug of Death*. I've plotted
the number of requests and errors below:

<div class="col-sm-12">
    <p align="center">
<img src="{{ "/assets/images/hug_of_death_lg.png" | prepend: site.url }}" alt="Plot of the traffic to my site." class="img-rounded img-responsive">
    </p>
</div>
  
Looking at this graph, it is interesting that the number of requests per
minute (blue line) seems to increase exponentially in the first 6 hours.
It starts to level off and eventually becomes linear and reaches a
maximum of about 400 requests/minute with peaks at almost 600 requests
per minute! I was not expecting that and I had planned my site to only
accomodate about 30 requests/minute.

When did the Hug of Death actually occur?
-----------------------------------------

As you can see from the plot above, the number of errors per minute (red
line) started spiking at 7 hours and then started increasing. **This was
the Hug of death - right at the 7-hour mark - at 200 requests per
minute.**

What exactly was happening at the 7-hour mark? There were three main
problems I started encountering. These included
`110: Connection timed out`, `104: Connection reset by peer`, and
`111: Connection refused` (as evidenced from the NGINX error logs). The
first errors to start occurring where the `110` errors (48% of all)
because the requests were taking so long that the Gunicorn workers
stopped the thread and started a new one. Long requests were the result
of having to search Youtube to make the music playlists (there was a way
around this, more on that below).

The second major error I started started getting were the `104` errors
(49% of all errors) from users disconnecting after starting a request.
Because requests were taking so long, people started thinking *"If I
reload this page, it should work faster!"* (well, who could blame
them?). That ended up messing up things even worse because there was a
startup time to the app each time this happened.

The third error occured more infrequently (3% of all errors) when I
tried to fix things. I'd take down the site to increase the wait time on
Gunicorn threads or decreasing number of Youtube searches which would
freeze everyone out of the site for a few seconds, resulting in the
`111` error.

What caused the sudden drop in the number of errors around hour 13?
-------------------------------------------------------------------

In the plot above you can see a sharp drop in the number of errors
around 13-hours. At this time I finally had the smart idea to introduce
[load balancing on my NGINX
server](http://nginx.org/en/docs/http/load_balancing.html). This is very
simple to do since I had already setup NGINX as a reverse proxy, but I
just hadn’t thought of it until 13 hours in… I took two other computers
I had at home (an 8-core desktop and a 4-core laptop) and deployed my
app to them, adding another 12 Gunicron workers. I then setup the main
server to proxy traffic to them so that I had about 15 Gunicorn workers
working simultaneously. This dramatically decreased the number of errors
but since I still had problems with bandwidth I continued to get a few
errors until the requests died down to about 200/minute.

What I should have done to prevent the Hug of Death
===================================================

I was pretty naive putting my simple, small, webapp on the internet
where a lot of people could access it at once. I’m glad I did, because I
got a lot of great feedback, but it probably put angered more people
than necessary who tried to access the site and constantly got the error
pages.

Benchmarking, beforehand
------------------------

I usually benchmark my sites to check for speed, but I had never
benchmarked my site with regards to concurrent users and load impact.
There are a number of great tools that can do this:

-   [Siege](https://github.com/JoeDog/siege) - command line utility
-   [LoadImpact](https://loadimpact.com/) - free and online
-   [wrk](https://github.com/wg/wrk) - command line utility

Had I used these, they could’ve shown me the error in my ways and forced
me to plan to use more servers as proxies from the beginning.

More caching, beforehand
------------------------

The main limitation I had was the HTTP GET requests to Youtube for
building playlists. I had planned on caching these for common searches,
but I should’ve done some of the caching beforehand so I didn’t have to
search *anew* for every request like I did here.

Epilogue
========

Since the *Hug of Death*, my site has levelled off and continues to see
a fair bit of traffic at a more consistent and manageable rate (I’ve
scaled down the number of Gunicorn workers now). Here is a plot of my
total traffic since inception:

<div class="col-sm-12">
    <p align="center">
<img src="{{ "/assets/images/total_requests_lg.png" | prepend: site.url }}" alt="Total number of requests to Music Suggestions Ninja" class="img-rounded img-responsive">
    </p>
</div>
       
There is a lot of hockey-stick growth in the very beginning, but now my
traffic has levelled off to about 3,900 requests per day which is more
than manageable.
