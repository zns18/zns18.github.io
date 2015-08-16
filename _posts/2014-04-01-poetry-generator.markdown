---
layout: post
title: Poetry Generator
date: 2014-04-01
comments: true
archive: false
tags: poetry, python, machine-learning, coding
coding: true
link: http://github.com/schollz/poetry-generator
---

A Python3 based Backus-Naur poetry generator. *Over 70 stars on Github!* [Source code here](https://github.com/schollz/poetry-generator).

This program works on the basis that every word in the English language is either "positive" or "negative." For instance "lovely" is positive and "thorn" is negative. A "poem" is a group of sentences that are structured in a way to have +1, -1 or 0 in terms of the positivity/negativity.  A "mushy poem" is strictly positive.

All the syntax and word choices are in the ```poem.bnf``` file. The main program is in ```poem.py``` and for web applications use the ```poem.php``` script to automatically generate a poem onload!

Try the [demo here](http://www.poetrygenerator.ninja)
