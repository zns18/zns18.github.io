---
layout: post
title: Simple SSH cluster
date: 2015-04-18
comments: true
archive: false
tags: clustering, python, ssh, coding
coding: true
link: http://github.com/schollz/beowulf_ssh_cluster
---

This program is a example of a Beowulf cluster or so-called Stone SouperComputer with Python and SSH. [Source code here](https://github.com/schollz/beowulf_ssh_cluster).

In this example, the server connects to any number of client computers via SSH and asks them to help compute some problem. Once a client finishes, the client sends back to the result to the server which stores the result on its own disk. The server then sends that client a new set of computations to finish, and this repeats until all the computations are finished. The client never has to store any information. The server is able to keep track of the client threads, the overall productivity and productivity of each client, and the entirety of the finished results.

I wrote an article about this [here](http://rpiai.com/2015/04/18/python-beowulf-cluster-find-a-use-for-those-computers-that-are-10-years-old).
