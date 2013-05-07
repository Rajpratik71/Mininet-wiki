(updated for Mininet 2.0.0)

by Bob Lantz, Nikhil Handigol, Brandon Heller, and Vimal Jeyakumar

This document is meant to give you a brief sense of what Mininet is and
how it works, including a basic introduction to Mininet's
Python API, the core of Mininet's functionality that you will usually want to use to create networks and run experiments.

- [What is Mininet?](#what)
- [Why is Mininet cool?](#why)
- [What are Mininet's limitations?](#limits)
- [Working with Mininet](#working)
 - [Creating Topologies](#creating)
 - [Setting Performance Parameters](#setting)
 - [Running Programs in Hosts](#running)
 - [New: `popen()`/`pexec()` interface](#popen)
 - [Host Configuration Methods](#config)
 - [Naming in Mininet](#naming)
 - [CLI](#cli)
 - [Additional Examples](#examples)
- [Mininet API Documentation](#api)
- [Measuring Performance](#measuring)
- [OpenFlow and Custom Routing](#openflow)
 - [OpenFlow Controllers](#controllers)
 - [External Controllers](#extcontrollers)
 - [Multipath Routing](#multipath)
- [Updating Mininet](#updating)
- [Learning Python](#python)
- [Useful Background Information](#background)
- [Useful Python Resources](#resources)
- [How Does It Really Work? (optional: for the curious)](#how)

<a id=what></a>

What is Mininet?
----------------

Mininet is a *network emulator*. It runs a collection of end-hosts,
switches, routers, and links on a single Linux kernel. It uses
lightweight virtualization to make a single system look like a complete
network, running the same kernel, system, and user code. A Mininet host
behaves just like a real machine; you can `ssh` into it (if you start up
`sshd` and bridge the network to your host) and run arbitrary programs.
The programs you run can send packets through what seems like a real
Ethernet interface, with a given link speed and delay. Packets get
processed by what looks like a real Ethernet switch, router, or
middlebox, with a given amount of queueing. When two programs, like an
`iperf` client and server, communicate through Mininet, the measured
performance should match that of two (slower) native machines.

<a id=why></a>

Why is Mininet cool?
--------------------

0.  It's fast - starting up a simple network takes just a few seconds.
	This means that your run-edit-debug loop can be very quick.

1.  You can create custom topologies: a single switch, larger
    Internet-like topologies, the Stanford backbone, a data center, or
    anything else.

2.  You can run real programs: anything that runs on Linux is available
    for you to run, from web servers, to TCP window monitoring tools, to
    Wireshark.
    
3.  You can customize packet forwarding: Mininet's switches are
    programmable using the OpenFlow protocol. Custom Software-Defined
    Network designs that run in Mininet can easily be transferred to
    hardware OpenFlow switches for line-rate packet forwarding.

4.  You can run Mininet on your laptop, on a server, in a VM, on a native
    Linux box (Mininet is included with Ubuntu 12.10+!), or in the cloud
    (e.g. Amazon EC2.)
    
5.  You can share and replicate results: anyone with a computer can run
    your code once you've packaged it up.
    
6.  You can use it easily: you can create and run Mininet experiments by
    writing simple (or complex if necessary) Python scripts.
    
7.	Mininet is an *open source project*, so you are encouraged to examine
    its source code on <https://github.com/mininet>, modify it, fix bugs,
    file issues/feature requests, and submit patches/pull requests.
    
8.  Mininet is under active development. So, if it sucks, doesn't make sense,
	or doesn't work for some reason, please let us know on `mininet-discuss`
	and the Mininet user and developer community can try to explain it,
	fix it, or help you fix it. :-) If you find bugs, you are encouraged to
	submit patches to fix them, or at least to submit an issue on github
	including a reproducible test case.

<a id=limits></a>

What are Mininet's limitations?
-------------------------------

Although we think Mininet is great, it does have some limitations.
For example,

- Running on a single system is convenient, but it imposes resource
  limits: if your server has 3 GHz of CPU and can switch about 3 Gbps
  of simulated traffic, those resourcess will need to be balanced and
  shared among your virtual hosts and switches.

- Mininet uses a single Linux kernel for all virtual hosts; this means
  that you can't run software that depends on BSD, Windows, or other
  operating system kernels.

- Mininet won't write your OpenFlow controller for you; if you need
  custom routing or switching behavior, you will need to find or develop
  a controller with the features you require.

- Currently Mininet doesn't do NAT out of the box. This means that
  your virtual hosts will be isolated from your LAN by default; this is
  usually a good thing, but means your hosts can't talk directly to
  the Internet unless you provide a means for them to do so.

- Currently all Mininet hosts share the host file system and PID space;
  this means that you may have to be careful if you are running daemons
  that require configuration in /etc, and you need to be careful that
  you don't kill the wrong processes by mistake.

- Unlike a simulator, Mininet doesn't have a strong notion of virtual
  time; this means that timing measurements will be based on real time,
  and that faster-than-real-time results (e.g. 100 Gbps networks) cannot
  easily be emulated.
  
With the exception of multiple kernels, most of these limitations are not
intrinsic to Mininet; eliminating them is simply a matter of code, and you
are encouraged to contribute any enhancements you may develop!

<a id=working></a>

Working with Mininet
--------------------

The following sections describe several features of Mininet (and its
Python API) which you may find useful while completing the assignments.

<a id=creating></a>

### Creating Topologies


Mininet supports *parametrized topologies*. With a few lines of Python
code, you can create a flexible topology which can be configured based
on the parameters you pass into it, and reused for multiple experiments.

For example, here is a simple network topology (based on
`mininet/topo.py:SingleSwitchTopo`) which consists of a specified number
of hosts (`h1` through `hN`) connected to a single switch (`s1`):

```python
	#!/usr/bin/python
	
	from mininet.topo import Topo
	from mininet.net import Mininet
	from mininet.util import dumpNodeConnections
	from mininet.log import setLogLevel
	
	class SingleSwitchTopo(Topo):
	    "Single switch connected to n hosts."
	    def __init__(self, n=2, **opts):
	        # Initialize topology and default options
	        Topo.__init__(self, **opts)
	        switch = self.addSwitch('s1')
	        # Python's range(N) generates 0..N-1
	        for h in range(n):
	            host = self.addHost('h%s' % (h + 1))
	            self.addLink(host, switch)
	
	def simpleTest():
	    "Create and test a simple network"
	    topo = SingleSwitchTopo(n=4)
	    net = Mininet(topo)
	    net.start()
	    print "Dumping host connections"
	    dumpNodeConnections(net.hosts)
	    print "Testing network connectivity"
	    net.pingAll()
	    net.stop()
	
	if __name__ == '__main__':
	    # Tell mininet to print useful information
	    setLogLevel('info')
	    simpleTest()
```

Important classes, methods, functions and variables in the above code
include:

`Topo`: the base class for Mininet topologies

`addSwitch()`: adds a switch to a topology and returns the switch name

`addHost()`: adds a host to a topology and returns the host name

`addLink()`: adds a bidirectional link to a topology (and returns a link key, but this is not important).  Links in Mininet are bidirectional unless noted otherwise.

`Mininet`: main class to create and manage a network

`start()`: starts your network

`pingAll()`: tests connectivity by trying to have all nodes ping each
other

`stop()`: stops your network

`net.hosts`: all the hosts in a network

`dumpNodeConnections()`: dumps connections to/from a set of nodes.

`setLogLevel( 'info' | 'debug' | 'output' )`: set Mininet's default output
level; 'info' is recommended as it provides useful information.

Additional example code may be found in [`mininet/examples`](https://github.com/mininet/mininet/tree/master/examples).

<a id=setting></a>

### Setting Performance Parameters

In addition to basic behavioral networking, Mininet provides performance
limiting and isolation features, through the `CPULimitedHost` and `TCLink`
classes.

There are multiple ways that these classes may be used, but one simple
way is to specify them as the default host and link classes/constructors
to `Mininet()`, and then to specify the appropriate parameters in the
topology. (You could also specify custom classes in the topology itself,
or create custom node and link constructors and/or subclasses.)

```python
	#!/usr/bin/python
	
	from mininet.topo import Topo
	from mininet.net import Mininet
	from mininet.node import CPULimitedHost
	from mininet.link import TCLink
	from mininet.util import dumpNodeConnections
	from mininet.log import setLogLevel
	
	class SingleSwitchTopo(Topo):
	    "Single switch connected to n hosts."
	    def __init__(self, n=2, **opts):
	        Topo.__init__(self, **opts)
	        switch = self.addSwitch('s1')
	        for h in range(n):
	            # Each host gets 50%/n of system CPU
	            host = self.addHost('h%s' % (h + 1),
		           cpu=.5/n)
	            # 10 Mbps, 5ms delay, 10% loss, 1000 packet queue
	            self.addLink(host, switch,
		           bw=10, delay='5ms', loss=10, max_queue_size=1000, use_htb=True)
	
	def perfTest():
	    "Create network and run simple performance test"
	    topo = SingleSwitchTopo(n=4)
	    net = Mininet(topo=topo, 
	                  host=CPULimitedHost, link=TCLink)
	    net.start()
	    print "Dumping host connections"
	    dumpNodeConnections(net.hosts)
	    print "Testing network connectivity"
	    net.pingAll()
	    print "Testing bandwidth between h1 and h4"
	    h1, h4 = net.get('h1', 'h4')
	    net.iperf((h1, h4))
	    net.stop()
	
	if __name__ == '__main__':
	    setLogLevel('info')
	    perfTest()
```

Important methods and parameters:

`self.addHost(name, cpu=f)`:

This allows you to specify a fraction of overall system CPU resources
which will be allocated to the virtual host.

`self.addLink( node1, node2, bw=10, delay='5ms', max_queue_size=1000,
loss=10, use_htb=True)`: 
adds a bidirectional link with bandwidth, delay and loss characteristics, with a maximum
queue size of 1000 packets using the Hierarchical Token Bucket rate limiter
and netem delay/loss emulator.  The parameter `bw` is expressed as a number in Mb/s; `delay` is expressed as a string with units in place (e.g. '5ms', '100us', '1s'); `loss` is expressed as a percentage (between 0 and 100); and `max_queue_size` is expressed in packets.

You may find it useful to create a Python dictionary to make it easy to
pass the same parameters into multiple method calls, for example:

	linkopts = dict(bw=10, delay='5ms', loss=10, max_queue_size=1000, use_htb=True)
	# alternately: linkopts = {'bw':10, 'delay':'5ms', 'loss':10,
	# max_queue_size=1000, 'use_htb':True}
	self.addLink(node1, node2, **linkopts)

This same technique `(**dict)` is useful for passing options to
Matplotlib and other libraries.

`net.get()`: retrieves a node (host or switch) object by name.
This is important if you want to send a command to a host (e.g. using
`host.cmd()`) and get its output.

Note: In the current master branch of Mininet, you can simply use
braces (e.g. `net['h1']`) to retrieve a given node by name.

<a id=running></a>

### Running Programs in Hosts

One of the most important things you will need to do in your experiments
is run programs in hosts, so that you can run more tests than the simple
`pingAll()` and `iperf()` tests which are provided by Mininet itself.

Each Mininet host is essentially a bash shell process attached to one or
more network interfaces, so the easiest way to interact with it is to
send input to the shell using the cmd() method.

To run a command in a host and get the output, use the `cmd()` method.

```python
	h1 = net.get('h1')	
	result = h1.cmd('ifconfig')
	print result
```

In many cases, you will wish to run a command in the background for a
while, stop the command, and save its output to a file:

```python
	from time import sleep
	...
	print "Starting test..."
	h1.cmd('while true; do date; sleep 1; done > /tmp/date.out &')
	sleep(10)
	print "Stopping test"
	h1.cmd('kill %while')
	print "Reading output"
	f = open('/tmp/date.out')
	lineno = 1
	for line in f.readlines():
	    print "%d: %s" % ( lineno, line.strip() )
	    lineno += 1
	f.close()
```

Note that we used the shell's output redirection feature to send output
to `/tmp/date.out`, the background execution feature `&` of the shell to
run the command in the background, and job control `kill %while` to shut
down the program that is running in the background. Unfortunately, if
you leave jobs running in the background they are not guaranteed to stop
if Mininet exits (either intentionally or due to an error), so you will
need to make sure that you stop all of your jobs cleanly. You may wish
to use the ps command periodically to make sure that no zombie jobs are
mindlessly marching onward and slowing down your EC2 instance.

(Note: Python strings may be delimited using either single or double
quotes. The example above uses double quotes in print statements and
single quotes for function arguments, but you can do whatever you like.
Python's print statement exists for convenience and possibly to help
BASIC programmers feel more at home.)

Having a shell process at your disposal makes it easy to perform other
tasks as well. For example, you can find out the PID of a background
command using

```python
	pid = int( h1.cmd('echo $!') )
```

Then you can wait for a particular process to finish execution by using
wait, e.g.:

```python
	h1.cmd('wait', pid)
```

Note that this will only work for UNIX commands and not for commands
(e.g. `while`, `cd`) which are built into the bash shell itself (and don't
have a separate pid!) Note also that each of these commands executed in
the foreground (no `&`) rather than the background (`&`) since we want
to get the output.

[Editorial: UNIX's (and `bash`'s) abbreviations and special characters (`$`, `!`, `&`) date back to the days of blazingly fast 300 bit per second "networks" and
Teletype™ terminals that printed on paper as you typed. Linux stuck with
them for compatibility and because humans still type slowly. `bash`
sometimes provides more verbose equivalents, if you wish to use them.]

In addition to using the shell's wait mechanism, Mininet itself allows
you to start a foreground command using `sendCmd()` and then wait for it
to complete at some later time using `waitOutput()`:

```python
	for h in hosts:
	    h.sendCmd('sleep 20')
	…
	results = {}
	for h in hosts:
	    results[h.name] = h.waitOutput()
```

If you are sending output to a file, you may wish to monitor that file's
contents interactively while your test is running. The
[`examples/multipoll.py`](https://github.com/mininet/mininet/tree/master/examples/multipoll.py)
example provides a function `monitorFiles()` which
implements one possible mechanism for monitoring multiple output files.
This simplifies the implementation of a test which interactively
monitors output from multiple hosts:

```python
	def monitorTest( N=3, seconds=3 ):
	    "Run pings and monitor multiple hosts"
	    topo = SingleSwitchTopo( N )
	    net = Mininet( topo )
	    net.start()
	    hosts = net.hosts
	    print "Starting test..."
	    server = hosts[ 0 ]
	    outfiles, errfiles = {}, {}
	    for h in hosts:
	        # Create and/or erase output files
	        outfiles[ h ] = '/tmp/%s.out' % h.name
	        errfiles[ h ] = '/tmp/%s.err' % h.name
	        h.cmd( 'echo >', outfiles[ h ] )
	        h.cmd( 'echo >', errfiles[ h ] )
	        # Start pings
	        h.cmdPrint('ping', server.IP(),
	                   '>', outfiles[ h ],
	                   '2>', errfiles[ h ],
	                   '&' )
	    print "Monitoring output for", seconds, "seconds"
	    for h, line in monitorFiles( outfiles, seconds, timeoutms=500 ):
	        if h:
	            print '%s: %s' % ( h.name, line )
	    for h in hosts:
	        h.cmd('kill %ping')
	    net.stop()
```

You may wish to run `multipoll.py` and look at its output.

Another example, [`examples/multiping.py`](https://github.com/mininet/mininet/tree/master/examples/multiping.py)
demonstrates a different (perhaps
simpler but less flexible) approach to monitoring standard output from a
host, using the `Node.monitor()` method, so you may wish to look at it as
well.

<a id=popen></a>

### New: `popen()`/`pexec()` interface

In addition to the shell-based mechanism of `cmd()`/`sendCmd()`, Mininet now
also supports a pipe-based interface which returns standard Python
`Popen()` objects (see Python's subprocess module for details.) This
mechanism is newer and not as well-tested as the `cmd()` mechanism, but
you may find it convenient for running multiple processes in the
background and monitoring their output. A `pmonitor()` function is
provided to make monitoring multiple `Popen()` objects even easier. 

> Warning: in Mininet 2.0.0, `pmonitor()` may return a number of blank
> lines after receiving EOFs. This should now be fixed in the master branch.

The code in [`examples/popenpoll.py`](https://github.com/mininet/mininet/tree/master/examples/popenpoll.py) implements the functionality similar to
what is described above, using the `popen()` interface and `pmonitor()`
helper function:

```python
	def pmonitorTest( N=3, seconds=10 ):
		"Run pings and monitor multiple hosts using pmonitor"
		topo = SingleSwitchTopo( N )
		net = Mininet( topo )
		net.start()
		hosts = net.hosts
		print "Starting test..."
		server = hosts[ 0 ]
		popens = {}
		for h in hosts:
	    	    popens[ h ] = h.popen('ping', server.IP() )
		print "Monitoring output for", seconds, "seconds"
		endTime = time() + seconds
		for h, line in pmonitor( popens, timeoutms=500 ):
	    	    if h:
	        	   print '%s: %s' % ( h.name, line ),
	    	    if time() >= endTime:
	        	   for p in popens.values():
	            	 p.send_signal( SIGINT )
		net.stop()
```

Note this implementation is slightly different since it pulls the time
management out of the helper function, but this enables `pmonitor()` to
catch `ping`'s output after it is interrupted.

Of course, you do not have to use `pmonitor()` - you can use
`Popen.communicate()` (as long as you don't have too many file
descriptors) or `select.poll()` or any other mechanism that works.

If you find bugs in the `popen()` interface, please let us know.

<a id=config></a>

### Host Configuration Methods

Mininet hosts provide a number of convenience methods for network
configuration:

1.  `IP()`: Return IP address of a host or specific interface.
2.  `MAC()`: Return MAC address of a host or specific interface.
3.  `setARP()`: Add a static ARP entry to a host's ARP
    cache.
4.  `setIP()`: Set the IP address for a host or specific interface.
5.  `setMAC()`: Set the MAC address for a host or specific interface

For example:

```python
    print "Host", h1.name, "has IP address", h1.IP(), "and MAC address", h1.MAC()
```

In each case, if you do not provide a specific interface (e.g. `h1-eth0`
or an interface object) the method will use the host's default
interface. The above functions are defined in mininet/node.py.

<a id=naming></a>

### Naming in Mininet

In order to use Mininet effectively, it is important to understand its
naming scheme for hosts, switches and interfaces. Usually, hosts are
called `h1..hN` and switches are called `s1..sN`. We recommend that you
follow this convention or a similar one. For clarity,
interfaces belonging to a node are named beginning with the node's name,
for example `h1-eth0` is host `h1`'s default interface, and `s1-eth1` is
switch `s1`'s first data port. Host interfaces are only visible from
within the host itself, but switch data ports are visible in the "root"
namespace (you can see them by typing ip link show in another window
while Mininet is running.) As a result, it's easy to examine switch
interfaces but slightly trickier to examine host interfaces, since you
must tell the host to do so (typically using `host.cmd()`.)

<a id=cli></a>

### CLI

Mininet includes a command-line interface (CLI) which may be invoked on
a network, and provides a variety of useful commands, as well as the
ability to display xterm windows and to run commands on individual nodes
in your network. You can invoke the CLI on a network by passing the
network object into the `CLI()` constructor:

```python
	from mininet.topo import SingleSwitchTopo
	from mininet.net import Mininet
	from mininet.cli import CLI
	
	net = Mininet(SingleSwitchTopo(2))
	net.start()
	CLI(net)
	net.stop()
```

Starting up the CLI can be useful for debugging your network, as it allows you to view the network topology (with the net command), test connectivity (with the pingall command), and send commands to individual hosts.

	*** Starting CLI:
	mininet> net
	c0
	s1 lo:  s1-eth1:h1-eth0 s1-eth2:h2-eth0
	h1 h1-eth0:s1-eth1
	h2 h2-eth0:s1-eth2
	mininet> pingall
	*** Ping: testing ping reachability
	h1 -> h2
	h2 -> h1
	*** Results: 0% dropped (0/2 lost)
	mininet> h1 ip link show
	746: lo: <LOOPBACK,UP,LOWER_UP> mtu 16436 qdisc noqueue state UNKNOWN
		link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
	749: h1-eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
		link/ether d6:13:2d:6f:98:95 brd ff:ff:ff:ff:ff:ff

Starting up the CLI can be useful for debugging your network, as it
allows you to view the network topology (with the `net` command), test
connectivity (with the `pingall` command), and send commands to individual
hosts.

<a id=examples></a>

### Additional Examples

Additional examples of Mininet scripts may be found in [`mininet/examples`](https://github.com/mininet/mininet/tree/master/examples).
You may find some of them to be useful and others (e.g. [`consoles.py`](https://github.com/mininet/mininet/tree/master/examples/consoles.py),
[`miniedit.py`](https://github.com/mininet/mininet/tree/master/examples/miniedit.py)) to be entertaining demonstrations.

Note: The examples are intended as instructional material to be read and understood, not as complete, out-of-the-box solutions to whatever problem you may have. You may be able to use some of the code with modification, but it's important to be able to examine and understand the code.

<a id=api></a>

### Mininet API Documentation

Mininet includes Python documentation strings for each module and API
call. These may be accessed from Python's regular `help()` mechanism. For
example,

```python
	python
	>>> from mininet.node import Host
	>>> help(Host.IP)
	Help on method IP in module mininet.node:
	
	IP(self, intf=None) unbound mininet.node.Host method
		Return IP address of a node or specific interface.
```

This same documentation is also available on the Mininet web site at
<http://mininet.github.com/api>.

You may wish to generate the HTML (and PDF) documentation yourself using `doxypy`:

    sudo apt-get install doxypy
    cd ~/mininet
    make doc
    cd doc
    python -m SimpleHTTPServer

At this point, you can point a web browser to port 8000 of the host that
Mininet is running on and browse the documentation for each of Mininet's
classes.

<a id=measuring></a>

### Measuring Performance

These are recommended, though you’re free to use any tool you’re
familiar with.

1.  Bandwidth (`bwm-ng`, `ethstats`)
2.  Latency (use `ping`)
3.  Queues (use `tc` included in `monitor.py`)
4.  TCP `CWND` statistics (`tcp_probe`, maybe we should add
    it to `monitor.py`)
5.  CPU usage (global: `top`, or per-container `cpuacct`)

<a id=openflow></a>

### OpenFlow and Custom Routing

One of Mininet's most powerful and useful features is that it uses
**Software Defined Networking**. Using the OpenFlow
(<http://openflow.org>) protocol and related tools, you can
program switches to do almost anything you want with the packets that
enter them. OpenFlow makes emulators like Mininet much more useful, since
network system designs, including custom packet forwarding with OpenFlow,
can easily be transferred to hardware OpenFlow switches for line-rate operation.
A tutorial for creating a simple learning switch using Mininet and OpenFlow may be
found at:

<http://www.openflow.org/wk/index.php/OpenFlow_Tutorial>

<a id=controllers></a>
#### OpenFlow Controllers

If you invoke Mininet from the command line, it uses the `ovsk` controller,
`ovs-controller`, by default. This controller implements a simple Ethernet
learning switch and supports up to 16 individual switches.

If you invoke the `Mininet()` constructor by itself, by default it uses the
`Controller()` class to create an instance of the Stanford/OpenFlow reference
controller, `controller`. Like `ovs-controller`, it turns your switches into
simple learning switches, but if you have installed `controller` using Mininet's
`install.sh -f` script, the patched version of `controller` should support a
large number of switches (up to 4096 in theory, but you'll probably max out
your computing resources much earlier.)

If you want to use your own controller, you can easily create a custom
subclass of `Controller()` and pass it into Mininet. An example can be seen
in `mininet.controller.NOX()`, which invokes NOX classic with a
set of modules passed in as options.

Here's a simple example of creating and using a custom POX `Controller` subclass:

```python
#!/usr/bin/python                                                                                      
                                                                                                       
from mininet.net import Mininet                                                                        
from mininet.node import Controller                                                                    
from mininet.topo import SingleSwitchTopo                                                              
from mininet.log import setLogLevel                                                                    
                                                                                                       
import os                                                                                              
                                                                                                       
class POXBridge( Controller ):                                                                         
    "Custom Controller class to invoke POX forwarding.l2_learning"                                     
    def start( self ):                                                                                 
        "Start POX learning switch"                                                                    
        self.pox = '%s/pox/pox.py' % os.environ[ 'HOME' ]                                              
        self.cmd( self.pox, 'forwarding.l2_learning &' )                                               
    def stop( self ):                                                                                  
        "Stop POX"                                                                                     
        self.cmd( 'kill %' + self.pox )                                                                
                                                                                                       
controllers = { 'poxbridge': POXBridge }                                                               
                                                                                                       
if __name__ == '__main__':                                                                             
    setLogLevel( 'info' )                                                                              
    net = Mininet( topo=SingleSwitchTopo( 2 ), controller=POXBridge )                                  
    net.start()                                                                                        
    net.pingAll()                                                                                      
    net.stop()      
```

Note that the above script is written so that it also can be used as a custom argument to `mn` for use with different topologies and tests as well as the Mininet CLI:

```
$ sudo mn --custom poxbridge.py --controller poxbridge --topo tree,2,2 --test pingall -v output
*** Ping: testing ping reachability
h1 -> h2 h3 h4 
h2 -> h1 h3 h4 
h3 -> h1 h2 h4 
h4 -> h1 h2 h3 
*** Results: 0% dropped (0/12 lost)
```

<a id=extcontrollers></a>
#### External OpenFlow Controllers

Although custom `Controller()` subclasses are most convenient for automatically starting and shutting down your controller, you may find it useful to connect Mininet to an existing controller that is running somewhere else, for example somewhere on your LAN, in another VM, or on your laptop. Using a`Controller()` class allows Mininet to automatically start up and shut down the controller as needed.

The `RemoteController` class acts as a proxy for a controller which may be running anywhere on the control network, but which must be started up and shut down manually or by some other mechanism.

You can use `RemoteController` from `Mininet`:

```python
Mininet( topo=topo, controller=lambda name: RemoteController( name, ip='127.0.0.1' ) )
```

You can also create multiple controllers and create a custom `Switch()` subclass which
connects to different controllers as desired:

```python

c0 = Controller( 'c0' )  # local controller
c1 = RemoteController( 'c1', ip='127.0.0.2' )  # external controller
cmap = { 's1': c0, 's2': c1, 's3': c1 }

class MultiSwitch( OVSSwitch ):
    "Custom Switch() subclass that connects to different controllers"
    def start( self, controllers ):
        return OVSSwitch.start( self, [ cmap[ self.name ] ] )
```

You can also specify an external controller from the `mn` command line:

    $ sudo mn --controller remote,ip=192.168.51.101

<a id=multipath></a>
#### Multipath Routing

It's important to remember that Ethernet bridges (also known as learning switches)
flood broadcast packets. This means that if your network has loops or multiple
paths in it, it will not work the default `ovs-controller` and `controller`
controllers, nor NOX's `pyswitch`, nor POX's `l2_learning`, which all act as
learning switches/Ethernet bridges.

In spite of the obviousness of this issue, it has become a
[[ Frequently Asked Question | FAQ#wiki-ethernet-loops ]].

If you are building a fat-tree like topology, you may wish to take a look at
[RipLPOX](http://github.com/brandonheller/riplpox), a basic datacenter
controller implemented using POX. You may be able to use it as a starting point
for your own custom multipath routing.

Exercise for the reader: Implement a custom `Controller()` subclass to invoke
RipLPOX.

<a id=updating></a>
### Updating Mininet

If we need to make changes or additions to Mininet to fix bugs or
other problems, and you have installed Mininet from source,
you may wish to update your copy of Mininet. This is easily done using:

	cd ~/mininet
	sudo make develop # this only needs to be done once
	git fetch
	git pull --rebase


<a id=python></a>

### Learning Python

Mininet is written in Python and allows Python-based user scripts to
interface with it. Fortunately Python is one of the easiest computer
languages to understand, learn, and use, due to its (mostly) readable
syntax, similarity to other object-oriented languages, and many useful
libraries. Once you reconcile yourself to its quirks (significant
indentation, mandatory use of `self`, runtime error checking, etc.) you
may appreciate it as a very quick (if sometimes dirty) way to crank out
useful scripts and code.

There are a vast variety of free Python tutorials available on the internet,
from books to complete courses. The Python documentation at
<http://docs.python.org> will also quickly become your friend if it isn't already.

In addition to helping to locate Python tutorials, Google seems to work 
astoundingly well for finding answers to Python questions, many of which
are answered on <http://stackoverflow.com>.

<a id=background></a>

### Useful Background for Using Mininet

(Random list, no particular order.)

1.  Object oriented programming (creating classes, objects from classes,
    etc.)

2.  Importing Python modules 

	<http://docs.python.org/tutorial/modules.html>

3.  Invoking system utilities from Python

	<http://docs.python.org/library/subprocess.html>
	<http://docs.python.org/library/os.html]>
	<https://github.com/amoffat/pbs>

4.  Parsing output files in your own format

	<http://docs.python.org/library/re.html>

5.  Passing command line arguments to your script

	<http://docs.python.org/dev/library/argparse.html>

6.  `matplotlib` for plotting graphs

	<http://matplotlib.sourceforge.net>

<a id=resources></a>

### Useful Python Resources

1.  The Python documentation is a good place to start:

	<http://docs.python.org/tutorial/index.html>

2.  Beginner’s guide:

	<http://www.python.org/about/gettingstarted>

<a id=how></a>

### How Does It Really Work? (optional: for the curious)

The magic behind Mininet’s illusion is a set of features built into
Linux that allow a single system to be split into a bunch of smaller
“containers”, each with a fixed share of the processing power, combined
with virtual link code that allows links with accurate delays and
speeds.

Internally, Mininet employs lightweight virtualization features in the
Linux kernel, including process groups, CPU bandwidth isolation, and
network namespaces, and combines them with link schedulers and virtual
Ethernet links. These features yield a system that starts faster and
scales to more hosts than emulators which use full virtual machines.

A Mininet network consists of the following components:

1.  *Isolated Hosts*. An emulated host in Mininet is a group of user-level
    processes moved into a network namespace - a container for network
    state. Network namespaces provide process groups with exclusive
    ownership of interfaces, ports, and routing tables (such as ARP and
    IP). For example, two web servers in two network namespaces can
    coexist on one system, both listening to private eth0 interfaces on
    port 80. Mininet uses CPU Bandwidth Limiting to limit the fraction
    of a CPU available to each process group.
    
2.  *Emulated Links*. The data rate of each link is enforced by Linux
    Traffic Control (`tc`), which has a number of packet schedulers to
    shape traffic to a configured rate. Each emulated host has its own
    virtual Ethernet interface(s) (created and installed with ip link
    add/set). A virtual Ethernet (or `veth`) pair, acts like a wire
    connecting two virtual interfaces, or virtual switch ports; packets
    sent through one interface are delivered to the other, and each
    interface appears as a fully functional Ethernet port to all system
    and application software.
    
3.  *Emulated Switches*. Mininet typically uses the default Linux bridge
    or Open vSwitch running in kernel mode to switch packets across
    interfaces. Switches and routers can run in the kernel (for speed)
    or in user space (so we can modify them easily).

For more details, see the 
[Mininet website](http://openflow.org/mininet) and our Mininet [Publications](publications).