---
layout: post
title: Beowulf cluster in less than 200 lines of code
date: 2015-04-18
comments: true
archive: false
tags: blog, web development, benchmarking, python, raspberry pi
blog: true
description: This is a simple, but useful, example of how you can take advantage of all the old computers you have laying around. Ever want to make a homemade supercomputer? This is the start.
---

*“He has thirty men’s heft of grasp in the gripe of his hand, the
bold-in-battle. Blessed God out of his mercy this man hath sent to Danes
of the West, as I ween indeed, against horror of Grendel”* - Beowulf

<br>
<div class="row">
<div class="col-md-1"></div>
<div class="col-md-10"><img src="/assets/images/computers.jpg" alt="A selection of the computers in my cluster" class="img-responsive center-block"></img>
<p>My Beowulf cluster contains two Raspberry Pi’s (Model B and Model B+), two 2004 Mac G4 iBooks loaded with Lubuntu, a Chromebook with crouton, a Intel i7 desktop with VirtualBox hosting Ubuntu, and a Intel Xeon virtual server with Ubuntu.</p></div>
<div class="col-md-1"></div>
</div>
<br>        
Introduction
============

This program is a example of a [Beowulf
cluster](http://en.wikipedia.org/wiki/Beowulf_cluster) or so-called
[Stone SouperComputer](http://www.extremelinux.info/stonesoup/) when
hooked up with antiquated computers. My program uses Python and SSH and
is very simple (\< 200 lines). However, there are pretty good libraries
like [MPI](http://www.open-mpi.org/) out there that can be used instead
for more intensive applications. When computers are in clusters they can
do cool things, like carry out [the great prime
search](http://www.mersenne.org/), or [solve the protein folding
problem](http://folding.stanford.edu/). My program is a simple way of
doing something similar, using basically any computer that can run at
least Lubuntu with very minimal coding.

How does it work? Basically, the server connects to any number of client
computers via SSH and asks them to help compute some problem. Once a
client finishes, the client sends back to the result to the server which
stores the result on its own disk. The server then sends that client a
new set of computations to finish, and this repeats until all the
computations are finished. The client never has to store any
information. The server is able to keep track of the client threads, the
overall productivity and productivity of each client, and the entirety
of the finished results.

The following information and code is readily available to download from
my [Github](http://github.com/schollz/beowulf_ssh_cluster).

Uses
====

Computation.
------------

Beowulf clusters can be used for computation. My cluster is more of a
Stone Soupercomputer - a cluster of antiquated computers - so some of
the computers will be exceedingly slow.

The example I've included here is a computational-use - the computation
of the first ten million primes. As an example of the disparity between
computers when using a Beowulf cluster of old and new, here is a plot of
the rate of prime searching for the computers in my cluster:

<br>
<div class="row">
<div class="col-md-1"></div>
<div class="col-md-10"><img src="/assets/images/primes_per_second.png" alt="The speed at which I can calculate primes" class="img-responsive center-block"></img>
</div>
<div class="col-md-1"></div>
</div>
<br>        
As you can see, you'd need about five 10-year-old computers to
equivalate a single modern desktop. Its probably just better to buy a
desktop if this is what you want. Modern computers are faster and more
energy efficient, so using antiquated computers for computation probably
is not a wise choice.

Web scraping.
=============

A Beowulf cluster is especially suited for scraping, since the
bottleneck is mainly the connection time to the website. Thus, the web
scraping with a Beowulf cluster will roughly scale with the number of
computers (or cores per computers). If you do use this for a web scraper
you might like to use [Tor](http://www.torproject.org/) so you don't get
blocked - I've included a code block that utilizes Tor. Here is a plot
of my results from the same computers I used above for scraping a
website with and without Tor:

<br>
<div class="row">
<div class="col-md-1"></div>
<div class="col-md-10"><img src="/assets/images/sites_per_minute.png" alt="The speed at which I can calculate primes" class="img-responsive center-block"></img>
</div>
<div class="col-md-1"></div>
</div>
<br>        
For this purpose, the slowest, older computers are only about two times
slower than he fastest, newest computers. And by combining their power,
you can basically recover the entirety of their might as shown by the
actual sites per minute gathered by the cluster versus the sum of
individual components:

<br>
<div class="row">
<div class="col-md-1"></div>
<div class="col-md-10"><img src="/assets/images/sum_total_vs_actual_total.png" alt="The speed at which I can calculate primes" class="img-responsive center-block"></img>
</div>
<div class="col-md-1"></div>
</div>
<br>        
Of course, you'll also see that Tor will slow you down quite a bit if
you choose to use that extra security boost.

**Note, if you do use this as a web scraper, be sure to read the Terms
of Service and make sure to follow `robots.txt` as some sites do not
allow web scraping (and be kind not to overload any servers with
thousands of connections per second).**

The Code
========

Server
------

```python
import shlex
from subprocess import Popen, PIPE
import logging
import threading
import time
import json
import pickle


cluster = {}
cluster['this computer']={'address':'username@127.0.0.1','cores':2}

# Generic worker thread for sending commands
def worker(cmd,i,):
    global response
    process = Popen(shlex.split(cmd), stdout=PIPE)
    (output,err)=process.communicate()
    exit_code = process.wait()
    response[i] = output

# Worker initiation protocol to send a command but wait until all threads are finished
def runAndWait(remote_cmd,clients):
    threads = []
    for i in range(len(clients)):
        cmd = "ssh "+clients[i]+" '"+remote_cmd+"'"
        print cmd
        t = threading.Thread(target=worker, args=(cmd,i,))
        threads.append(t)
        t.start()
    for i in range(len(clients)):
            threads[i].join()       
    print '[' + cmd + ']: DONE'

# Worker initiation protocol to send a file and wait until all files are sent
def sendFileAndWait(file,folder,clients):
    threads = []
    for i in range(len(clients)):
        cmd = "scp " + file + " " + clients[i] + ":" +folder+"/"
        print cmd
        t = threading.Thread(target=worker, args=(cmd,i,))
        threads.append(t)
        t.start()
    for i in range(len(clients)):
            threads[i].join()       
    print '[' + cmd + ']: DONE'


# Generate list of clients (add extra if there are extra cores available)
clients = []
for computer in cluster:
    for i in range(int(cluster[computer]['cores'])):
        clients.append(cluster[computer]['address'])

# Initialize responses
response = []
for i in range(len(clients)):
    response.append(clients[i])

# Initialize logging
logging.basicConfig(level=logging.DEBUG,
    format='%(asctime)s %(name)-12s %(levelname)-8s %(message)s',
    datefmt='%y-%m-%d %H:%M:%S',
    filename='server.log',
    filemode='a'
)

# First make the distributed folder if it doesn't exist
runAndWait('mkdir beowulf',clients)

# Second, send the file to run
sendFileAndWait('client_primes.py','beowulf',clients)

# Start up tor (if needed)
#runAndWait('base64 -d ~/pass | sudo -S /etc/init.d/tor restart',clients)


# Initialize the threads with a simple task
threads = []
clientDiag = []
for i in range(len(clients)):
    response[i]=""
    remote_cmd = 'ls -l | tail -n 1'
    cmd = "ssh "+clients[i]+" '"+remote_cmd+"'"
    t = threading.Thread(target=worker, args=(cmd,i,))
    threads.append(t)
    t.start()
    clientDiag.append({})
    clientDiag[i]['server'] = clients[i]
    clientDiag[i]['lastSeen'] = time.time()
    clientDiag[i]['totalIterations'] = 0
    clientDiag[i]['timePerIteration'] = 0


# Start main thread
logger = logging.getLogger('main')
start = 1
finish = 10000000
blocks = 10000 # number of blocks to do each time. Try to block size ~1-5 seconds of computation time to take advantage
master_index = start
startTime = time.time()
while master_index<finish:
    for i in range(len(clients)):
        if not threads[i].isAlive():
            # Get response
            try:
                json_object = json.loads(response[i])
                # Save whatever data you want from the response
                for key in json_object.keys():
                    if json_object[key]:
                        with open('primes','a') as f:
                            f.write(str(key))
                            f.write('n')
            except:
                print "No JSON object detected"

            # Update the client meta-data
            clientDiag[i]['lastSeen'] = time.time()
            clientDiag[i]['totalIterations'] += blocks
            clientDiag[i]['timePerIteration'] = (time.time()-startTime)/clientDiag[i]['totalIterations']
            if clientDiag[i]['totalIterations'] % (blocks*5)==0:
                logger.debug(clientDiag[i])

            # Send the next set
            # Firt argument is Tor flag
            remote_cmd = 'python beowulf/client_primes.py 0 ' + ' '.join(str(x) for x in range(master_index,master_index+blocks))
            cmd = "ssh "+clients[i]+" '"+remote_cmd+"'"
            threads[i] = threading.Thread(target=worker, args=(cmd,i,))
            threads[i].start()
            master_index += blocks

            # Log every 20 blocks
            if (master_index-start) % (blocks*20) == 0:
                logger.debug(str((time.time()-startTime)/(master_index-start+0.0)) + ' seconds per iteration (TOTAL)')
                logger.info('Finished up to ' + str(master_index))

    time.sleep(.05)
```

Client
------

```python
import json
import sys

if int(sys.argv[1])==1:
    import urllib2
    try:
        import socks
    except:
        print "you need to install PySocks for Tor use"
    import socket
    socks.set_default_proxy(socks.SOCKS5, "127.0.0.1", 9050)
    socket.socket = socks.socksocket
    #my_ip = urllib2.urlopen('http://icanhazip.com').read()

def is_prime(n):
    if n = 2
    if n % 2 == 0 or n % 3 == 0:
        return False
    for i in xrange(5, int(n ** 0.5) + 1, 6):
        if n % i == 0 or n % (i + 2) == 0:
            return False
    return True

def worker(*nums):
    results = {}
    for n in nums[0]:
        results[int(n)] = is_prime(int(n))
    return results

print json.dumps(worker(sys.argv[2:]))
```

How it works
============

Server
------

The server computer needs to have SSH access to each of the computers in
the cluster using a SSH key (to alleviate having to store passwords in
the program). You can setup SSH pretty easily by installing
`openssh-client` on each of the cluster computers. The cluster computers
can be LAN or Public, as long as you have access to them via SSH and
they can run Linux. Also make sure to install whatever Python libraries
you'll need on the corresponding computers, as this program doesn't do
that yet.

To generate a SSH key on the server computer, simply use

```bash
ssh-keygen
```

And just press enter/enter/enter.

Then copy the ssh key to all your connected computers using

```bash
ssh-copy-id user@address
```

And type in your password. That should be the only time you need to type
in your password. Then, if the clients have the right python libraries,
you can run the server program `python server_primes.py`, to
automatically make the directories and transfer the client program and
run the client program.

The server sends a SSH command to run a command on the client computer.
Do not send anything private to the client, because it is run as a
command and might be saved in the client history. The client then runs
the program and sends back the output as a JSON which I believe is
secured through SSH.

### Tor

I included the ability to add in Tor connections. The client script uses
the first argument as a Tor flag. To use Tor you need to install tor
`apt-get install tor` as well as
[PySocks](https://github.com/Anorov/PySocks) `pip install PySocks` on
the client computers.

Since Tor needs to run as a super user, you can create store your
password on your client. Since its never a good idea to store the
plaintext password, I suggest using base64. I.e. Type your password into
`~/pass` and then

```bash
base64 ~/pass > ~/pass2
mv ~/pass2 ~/pass
```

The program is then set to use this password for running the Tor
connections.

Have fun!

  
  