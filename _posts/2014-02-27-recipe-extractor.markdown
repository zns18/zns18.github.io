---
layout: post
title: Recipe extractor
date: 2014-02-27
comments: true
archive: false
tags: poetry, python, machine-learning, coding, web
coding: true
link: https://github.com/schollz/extract_recipe
---

Extract recipes from websites, calculates cooking times, collects nutrition info from USDA database. [Source code here](https://github.com/schollz/extract_recipe).

The internet is full of recipes. A recipe is really only the *directions* and the *ingredients*, but for any given recipe website you always get a lot more information thatn you want (comments, links, advertisements). For instance the page for [Alton Brown's waffle](http://www.foodnetwork.com/recipes/alton-brown/basic-waffle-recipe.html) recipe looks like this:

![Website example](https://rpiai.files.wordpress.com/2015/02/webcontentgrabber-01.png)

How can you just pull out the directions and ingredients, in a tag-independent way that can be used across any website? Learn how I did it [here](https://github.com/schollz/extract_recipe).
