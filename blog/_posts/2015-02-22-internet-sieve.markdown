---
layout: post
title: Simple internet content sieve
date: 2015-02-22
comments: true
archive: false
tags: python, coding, web scraping
blog: true
description: You can grab the html of a website, but what if you are only interested in a small part of the content of the website? Use a sieve!
---

The internet is full of useful information. However, the internet is also full of useless information. An AI can use the internet to its advantage, but only if it can appropriately figure out what information on a webpage is useful and what information is useless. How to do this?

Lets say you have an AI that wants to grab recipes by googling them and following the links to extract the recipes. Lets say it comes along to [Alton Brown's recipe for waffles](http://www.foodnetwork.com/recipes/alton-brown/basic-waffle-recipe.html), and you only want the recipe - i.e. just the *directions* and *ingredients*? As you can see below, most of the website is completely extraneous, so how can a computer know that?

<div class="col-sm-12">
    <p align="center">
<img src="{{ "/assets/images/webcontentgrabber-01.png" | prepend: site.url }}" alt="Website example" class="img-rounded img-responsive" width="90%">
    </p>
</div>

Of course you could write a spider that would crawl the tags and pull out the corresponding tags. But what if there are no "ingredient" or "directions" tags? What if you want to do this on a bunch of websites where the html structure is certainly apt to change?

To solve this problem, I recently wrote a handy script that I call the [Web Content Grabber](https://github.com/schollz/webcontentgrabber) (until I come up with a better name, that is), that does the job perfectly. Check out what happens when you run the [Web Content Grabber](https://github.com/schollz/webcontentgrabber) on [the waffle recipe](http://www.foodnetwork.com/recipes/alton-brown/basic-waffle-recipe.html):

```
Directions

Preheat waffle iron according to manufacturer's directions.
In a medium bowl whisk together the flours, soda, baking powder, salt, and sugar. In another bowl beat together eggs and melted butter, and then add the buttermilk. Add the wet ingredients to the dry and stir until combined. Allow to rest for 5 minutes.
Ladle the recommended amount of waffle batter onto the iron according to the manufacturer's recommendations. Close iron top and cook until the waffle is golden on both sides and is easily removed from iron. Serve immediately or keep warm in a 200 degree F oven until ready to serve.
Recipe courtesy Alton Brown, 2005
```

```
Ingredients

 * 4 3/4 ounces all-purpose flour, approximately 1 cup
 * 4 3/4 ounces whole-wheat flour, approximately 1 cup
 * 1/2 teaspoon baking soda
 * 1 teaspoon baking powder
 * 1 teaspoon salt
 * 3 tablespoons sugar
 * 3 whole eggs, beaten
 * 2 ounces unsalted butter, melted
 * 16 ounces buttermilk, room temperature
```

This web content grabber skips all the extraneous ads, comments, other food links and left only the content that I was interested. Its a nice little internet sieve.

## How it works

First it grabs the Markdown-formatted text of the html page using [html2text](https://github.com/aaronsw/html2text). It then searches through for the number of lines with any number of a given set of content-related words. What are the content related words? They are words corresponding to the block of content that you want. For instance, for extracting the directions above I used 'cooking' related words like "oven" and "stir" and "measuring cup" and "mix" and so on.

When you look at the straight text of a website, line by line, and count the number of content related words in each line you get a plotl like this:

<div class="col-sm-12">
    <p align="center">
<img src="{{ "/assets/images/enu0SNA.jpg" | prepend: site.url }}" alt="Website example" class="img-rounded img-responsive" width="90%">
    </p>
</div>

The peak that emerges is the directions for the recipe on the website. That content can then be efficiently extracted using a single-gaussian curve fitting algorithm to fit the peak. Then you know exactly what lines your content is on and you can extract them so then you have some automatically determined lines with content:

<div class="col-sm-12">
    <p align="center">
<img src="{{ "/assets/images/ju9eboZ.png" | prepend: site.url }}" alt="Contextual peaks" class="img-rounded img-responsive" width="90%">
    </p>
</div>

I wrote this in a very general way, so its possible to completely reconfigure for your own content extraction! You can see more info at the [Github](https://github.com/schollz/webcontentgrabber) with the code. Drop me a line if you have trouble or have ideas for more features!