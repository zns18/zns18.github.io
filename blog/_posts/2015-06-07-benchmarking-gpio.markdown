---
layout: post
title: Benchmarking the Raspberry Pi GPIO I/O Speed
date: 2015-06-07
comments: true
archive: false
tags: blog, web development, benchmarking, python, raspberry pi
blog: true
description: How fast can you read and write the GPIO pins on a Raspberry Pi? The results will surprise you!
---

A good question to ask yourself before using the Raspberry Pi GPIO pins
for time sensitive things is **exactly how fast can I read and write to
these pins**? Writing from, or sending signals, from the GPIO pins has
[already been investigated], but the other way around has been more
obscure. Here I answer the question and find some interesting,
unexpected results!

The test
========

All the following tests were done as root in a Raspberry Pi Model B+
using a Jessie build of Raspbian (Raspberry Pi Model 2 is generally [2-3
times faster]). To calculate result rates, the commands were run using
time (e.g. `time program`) and the `real` time result was used to divide
into the total number of cycles or iterations to figure out the
resulting rate.

How fast can you write to GPIO pins for outputing a signal?
===========================================================

I decided to test the two fastest languages - Python and Native C - to
see how fast it could be done. Also, I included Go in the experiments,
which is a language that has not been tested for generating square waves
yet. Here are the results.


<div class="table-responsive">
<table class="table table-bordered table-striped">
<thead>
<tr class="header">
<th align="left">Language (linked to code)</th>
<th align="left">Tested</th>
<th align="left">Result (cycle rate)</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><a href="https://github.com/schollz/raspberrypi_notes/blob/master/gpio_tests/writing/gpio_write.py">Python</a></td>
<td align="left">06/06/2015</td>
<td align="left">69 kHz</td>
</tr>
<tr class="even">
<td align="left"><a href="https://github.com/schollz/raspberrypi_notes/blob/master/gpio_tests/writing/gpio_write.go">Go</a></td>
<td align="left">06/06/2015</td>
<td align="left">174 kHz</td>
</tr>
<tr class="odd">
<td align="left"><a href="https://github.com/schollz/raspberrypi_notes/blob/master/gpio_tests/writing/gpio_write.c">C</a></td>
<td align="left">06/06/2015</td>
<td align="left">15 MHz</td>
</tr>
</tbody>
</table>
</div>

My results for Python and C are similar to what was [already
investigated], but the Go result is new! The Go result is pretty
unexpected - it seems to be the fastest language for writing to pins
with the exception of C. I think this is thanks to a [great library] by
[Stian Eikeland].

How fast can you read from GPIO pins *to memory*?
=================================================

Another question is how fast can you read? In this case I want to read a
pin many times and store the value in virtual memory so there is no
hard-disk storage here (which may be a bottleneck). I’m going to focus
on the two fastest languages - Go and native C.


<div class="table-responsive">
<table class="table table-bordered table-striped">
<thead>
<tr class="header">
<th align="left">Language (linked to code)</th>
<th align="left">Tested</th>
<th align="left">Result (read rate)</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><a href="https://github.com/schollz/raspberrypi_notes/blob/master/gpio_tests/reading/tovariable/read_in_memory.go">Go</a></td>
<td align="left">06/06/2015</td>
<td align="left">1.6 Mhz</td>
</tr>
<tr class="even">
<td align="left"><a href="https://github.com/schollz/raspberrypi_notes/blob/master/gpio_tests/reading/tovariable/read_in_memory.c">C</a></td>
<td align="left">06/06/2015</td>
<td align="left">7.2 MHz</td>
</tr>
</tbody>
</table>
</div>


Is reading harder or easier than writing for a given language? For C,
reading actually seems to be harder, as it reads about half the rate it
can write a cycle - so detecting pin changes is 4x slower than writing
pin changes. However, **Go can read faster than it can write**! That’s
pretty unexpected. Go seems like a good alternative to C for reading
GPIO pins.

These read speeds are effectively the *fastest* you can possibly read.
These programs read into memory and do nothing with the result. In a
practical case you’d want to use the result - most likely by writing it
to disk, thus you’ll have a bottleneck there immediately. This brings me
to the final question:

How fast can you read from GPIO pins *to disk*?
===============================================

This experiment is similar to the previous, except that here I use Go
and C to compile the individual bits into bytes which are written to
disk as a block every 1000 Bytes. The results are surprising:



<div class="table-responsive">
<table class="table table-bordered table-striped">
<thead>
<tr class="header">
<th align="left">Language (linked to code)</th>
<th align="left">Tested</th>
<th align="left">Result (read + write-to-disk rate)</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><a href="https://github.com/schollz/raspberrypi_notes/blob/master/gpio_tests/reading/tofile/read_to_file.go">Go</a></td>
<td align="left">06/06/2015</td>
<td align="left">440 kHz</td>
</tr>
<tr class="even">
<td align="left"><a href="https://github.com/schollz/raspberrypi_notes/blob/master/gpio_tests/reading/tofile/read_to_file.c">C</a></td>
<td align="left">06/06/2015</td>
<td align="left">673 kHz</td>
</tr>
</tbody>
</table>
</div>



Here was the real surprise - Go creeps up on C in terms of speed! Go
seems to be optimized for both *reading the GPIO pin* as well as
continuous *writing to disk*. Given the ease of coding, it seems like
the Go may be the best way to go for reading GPIO pins.

TL;DR
=====

Use Go for reading GPIO pins because its about as fast as C for
practical uses and a bit easier to code in, but use C if you want to
write to pins (max cycle rate 15 MHz) or if you just want to keep things
in memory.

  [already investigated]: http://codeandlife.com/2012/07/03/benchmarking-raspberry-pi-gpio-speed/
  [great library]: https://github.com/stianeikeland/go-rpio
  [Stian Eikeland]: https://github.com/stianeikeland
  [already been investigated]: http://codeandlife.com/2012/07/03/benchmarking-raspberry-pi-gpio-speed/
  [2-3 times faster]: http://codeandlife.com/2015/03/25/raspberry-pi-2-vs-1-gpio-benchmark/
  
  