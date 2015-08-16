---
layout: post
title: Faster string matching
date: 2015-05-03
comments: true
archive: false
tags: blog, web, development, strings
blog: true
description: This is a simple and robust way to do really fast string matching in Python (or any language, really).
---

The problem
===========

I constantly find myself in a situation, like developing the Music
Suggestions Ninja, where I need to have the user enter a string and find
the best match for that string from a list of strings. In order to
compensate for misspellings I’ve found the [fuzzywuzzy
package](https://github.com/seatgeek/fuzzywuzzy) very helpful. But how
about speed? Its slow to do that over 100,000+ words.

String matching
=================

So given I have a list of band names, how on earth do I search for one?
I want to allow for mispellings because I want to always at least *try*
to find something. This turns out, to be pretty easy. There is a pretty
reliable and fast algorithm for computing the [Levenshtein
distance](https://en.wikipedia.org/wiki/Levenshtein_distance) which can
essentially tell you the edit distance between two words.

There are problems with just using edit distance, though, as the edit
distance can be quite misleading if the words are only just rearranged.
However, someone smart has already thought of this and fixed it! The
solution is to use the Python package `fuzzywuzzy` which can compute
Levenshtein distances and account for all sorts of stuff, like
rearrangements.

So I like to use two metrics, as I found they work better than one, so
my string matching code is simply:

``` python
from fuzzywuzzy import fuzz
from fuzzywuzzy import process
import Levenshtein

def compareStrings(strings):
leven1 = fuzz.token_set_ratio(strings[0],strings[1])
leven2 = Levenshtein.ratio(str(strings[0]),str(strings[1]))
return (strings[0],strings[1],leven1,leven2)
```

Now how to get the best match? Here’s another problem: their can be a
lot of band names so its very slow to use a single loop. Here’s the
solution: use more computer power! We can actually use the
multiprocessing library to efficiently go through the band names, such
as:

``` python
import operator
from multiprocessing import Pool

def printTopTenResults(searchString):

stringList = []
for bandName in bandNames:
stringList.append((searchString,bandName))

pool = Pool(5)

results = pool.map(compareStrings, stringList)
print (sorted(results, key=operator.itemgetter(2, 3), reverse=True))[:10]
```

Try it out! Combine the two and test it with a tiny list of band names:

``` python
bandNames = ['Lynyrd Skynyrd','The Sex Pistols','Van Halen','Metric','Prince','Kings of Leon','The Beatles','The Monkees','Van Morrison','Jim Morrison']
printTopTenResults('Van Morrison')
```

So if you search for “Van Morrison” you’ll get a top result of
`('Van Morrison', 100, 1.0)` followed by `('Jim Morrison', 80, 0.75)`,
and `(Van Halen', 50, 0.47619047619047616)` as the respective scores
(second and third in tuple) decreases. It works pretty well! I’m happy
with it at least - its fast, efficient, precise, and scalable.

Faster string matching
========================

To solve this one can use a dictionary of spliced strings - basically a
hash table of string doublets. For example a word like “pillow” is
composed of `['pi','il','ll','lo','ow']` doublets. To make the hash
table, each doublet then is put into a dictionary where each of the
entries map back to the word, ‘pillow’.

To use this hash table, you can then take an input string, find the
string doublets and then get a new list of “most likely” strings that
correspond to the values in though string doublet keys. Searching
through those is very likely a very small fraction of the entire list,
thus it takes a small fraction of the time! I also use a multiprocessing
pool to speed up this part of the search.

My code for trying this out is on
[Github](https://github.com/schollz/string_matching).

Try it out!
===========

Download the code [here](https://github.com/schollz/string_matching) and
try it out for yourself.

Initial Setup
-------------

First download a wordlist. The example I use below uses [this
one](http://www-personal.umich.edu/~jlawler/wordlist.html) which you can
download from the terminal with:

```
wget http://www-personal.umich.edu/~jlawler/wordlist
```

You can use whatever wordlist you want, just make sure you change the
`WORDLIST_FILE` variable in `setup.py` (and if you have a different
format to your file, change how it loads in the words).

Then install the dependencies using `pip`:

```
pip install fuzzywuzzy python-Levenshtein
```

Examples
--------

After you finished the setup, finally run the `setup.py` to generate the
pickle files.

Then you can test the speed with

```
python test_string_matching WORD
```

For example, searching for “pellow”:

```
python test_string_matching pillow

trying with pillow
checking if it is exactly in list...
not found exactly
trying list look-up took 1.59740447998e-05

searching through entire list...
Top 5 matches:  pillow(200.0)  billow(166.3)  willow(166.3)  pill(160.0)  plow(160.0)
Best match: Pillow
searching through entire list took 1.50267004967

searching through hashed list...
Top 5 matches:  pillow(200.0)  willow(166.3)  billow(166.3)  pill(160.0)  plow(160.0)
Best match: Pillow
searching through hashed list took 0.111702919006
```

Of course the fastest is direct lookup, but in this case “pillow” wasn’t
in the wordlist (it is actually “Pillow” in the list). Matching through
the entire list takes 1.5 seconds, while the hashed version only takes
111 milliseconds! Way beter. The results for both are similar, as well,
getting next results that make sense (billow, willow, pill).

What happens if you mispell a word?

```
python test_string_matching.py madgascar

trying with madgascar
checking if it is exactly in list...
not found exactly
trying list look-up took 1.50203704834e-05

searching through entire list...
Top 5 matches:  madagascar(189.7)  madagascan(168.2)  marasca(150.0)  mascara(150.0)  alnaschar(133.7)
Best match: Madagascar
searching through entire list took 1.39013600349

searching through hashed list...
Top 5 matches:  madagascar(189.7)  madagascan(168.2)  mascara(150.0)  marasca(150.0)  lascar(133.7)
Best match: Madagascar
searching through hashed list took 0.112021923065
```

Both full list method and the hash method got the right result, but
again the hashed version was 14x faster!

Use it!
=======

To use it yourself, simply make your pickle files as done in the
`setup.py` file and then import the `string_matching.py` into your
project. Eventually I could make this an actual Python package, but I
don’t have the expertise for that yet.

[Source code](/2015/05/faster-strings/)
