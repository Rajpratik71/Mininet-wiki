Please add helpful **Frequently Asked Questions (FAQs)** and **high-quality answers (HQAs)** below.

Before you send a question to `mininet-discuss`, make sure your question isn't already in the FAQ -- and if you see a question asked repeatedly, feel free to add the answer to this FAQ!

*Since github wikis don't automatically generate a Table of Contents, please follow the existing format and add 1) an anchor for your question and 2) a link to it at the top of the page. Thanks!*

<!-- this doesn't work yet, alas :(
[[_TOC_]]
-->
### Start Here

* [How can I **get started** with Mininet?](#get-started)

### VM and Installation Questions

* [What is the **login/password** for the Mininet VM?] (#password)
* [Why can't I get **X11 forwarding** to work? I get `cannot open display:` or `$DISPLAY not set`, and `wireshark` doesn't work! `xterm` doesn't work either!](#x11-forwarding)
* [X11 forwarding is too hard! Can't I just **run a GUI in my VM** console window?](#vm-console-gui)
* [Can I run a **GUI/X11 application** within a Mininet host?](#vm-gui-mininethost)
* [How can I do a **native install** of Mininet?](#native-install)
* [Help! I am getting an **error from VirtualBox and my VM won't boot**!](#virtualbox-error)
* [Help! The **VM console screen is blank**!](#blank-screen)

### Using Mininet

* [How do I **run Linux programs** on my Mininet hosts?](#run)
* [How do I figure out the **command-line options** for the `mn` command?](#command-line-options)
* [How do I **use Mininet's Python API**?](#python-api)
* [Why can't I **ping Google** from my Mininet hosts?/How can I set up **NAT**?] (#NAT)
* [Why do the **switch data ports** have **random MAC addresses**? How do I assign MAC and IP addresses to the switch data ports?](#assign-macs)
* [Why does my network fail if I use **more than 16 switches**?](#ovs-controller)
* [How can I **control Mininet hosts remotely**?](#remote-control)
* [How can I add a **REST API** to Mininet?](#rest)
* [How do I run the **Mininet examples**?](#examples)
* [Why doesn't `dpctl` work? How can I **dump a switch's flow table**?](#dpctl)

###  OpenFlow Questions

* [Which **versions of OpenFlow** does Mininet support?](#openflow-versions)
* [What **OpenFlow switch implementations** does Mininet support?](#openflow-switches)
* [How can I use **multiple controllers** in my network?](#multiple-controllers)

### Troubleshooting

* [Help! I **updated my Ubuntu kernel** and now Open vSwitch won't start!](#ovs-reconfig)
* [Why does my controller, which implements an **Ethernet bridge or learning switch**, not work with my network which has **loops** in it? I can't ping anything!](#ethernet-loops)

### Advice for the Desperate and/or Clueless!

* [Help! **I don't understand OpenFlow** or SDN!](#openflow)
* [Help! **I have never used Linux or Unix before!**](#linux)
* [Help! **I don't understand networking** (or maybe computers) at all!!](#networking)
* [How do I **use (some Linux command)**?](#man)
* [Will you **do my (home)work assignment** for me? It's due next Tuesday at 4pm!](#homework)

***
<a id=get-started></a>
### How can I **get started** with Mininet?

Quick answer: Follow the steps on our [[Documentation]] page!

The best way to get started with Mininet is to install our ready-to-run virtual machine image as per our [Download](http://mininet.github.com/download) instructions, then go through the [Walkthrough](http://mininet.github.com/walkthrough), and then continue with the other steps on our [[Documentation]] page. You may find the [[Introduction to Mininet]] and the [[OpenFlow tutorial | http://www.openflow.org/wk/index.php/OpenFlow_Tutorial]] to be particularly useful in helping you to understand and use Mininet and OpenFlow/Software-Defined Networking, respectively.

***
<a id=password></a>
### What is the login/password for the Mininet VM?

As of Mininet 2.0.0, it is currently:

    mininet-vm login: mininet
    Password: mininet

On some older VMs it was `openflow`/`openflow`.

***
<a id=x11-forwarding></a>
### Why can't I get X11 forwarding to work? I get `cannot open display:` or `$DISPLAY not set`, and `wireshark` doesn't work! `xterm` doesn't work either!

**This is not a Mininet problem**. It means that **X11 forwarding is not set up correctly**.
First, consult the X11 setup instructions in the [OpenFlow Tutorial](http://www.openflow.org/wk/index.php/OpenFlow_Tutorial), including:

* Download X11
* Install X11
* Start up X11 server
* Access VM via `ssh`

Make sure you have carefully followed the necessary steps.
If things are still not working for you, you will want to make sure that:

1. Your **X11 server** on your client machine (e.g. your laptop) is installed correctly and is actually running
2. You are connecting via `ssh` using **X11 forwarding** (e.g. `ssh -X` on OS X/Linux or enabling X11 forwarding in a Windows `ssh` client like PuTTY or SecureCRT.)
3. You don't have any options in your client `.ssh/config` which interfere with X11 forwarding

When you log in with `ssh`, your `$DISPLAY` environment variable is set
X11 terminology is a bit confusing because the X11 server is actually run on the ssh client machine! The ssh client connects to the sshd server, which in turn forwards connections from X11 client applications (such as wireshark) to the local X11 server (usually running on your laptop or whatever machine is sitting in front of you.)

One note: if you have disabled IPv6, you may find that you need to add `AddressFamily inet` to your `/etc/ssh/sshd_config`.

Other unlikely causes of breaking X11 forwarding include it being disabled in `/etc/sshd_config` or disabled by SELinux. Neither of these should be the case in the Mininet VM image we provide.
You may wish to invoke debug logging on your `ssh` client to see why X11 forwarding isn't working. On OS X and Linux, you can use

    ssh -X -v <VM's IP address>

to see where the X11 forwarding is failing.

By default `ssh -X` times out after a while - you may prefer `ssh -Y` for that reason, although it is less secure.

There is **a wealth of information on the internet** explaining how to set up X11 forwarding correctly on any platform. This is easily found using Google or the search engine of your choice.

As an alternative to X11, you could also use VNC, but that is probably about as complicated as X11 and is left as an exercise to the reader.

**If this seems too complicated, you can simply run X11 in the VM console window as described below!**

***
<a id=vm-console-gui></a>
### X11 forwarding is too hard! Can't I just run a GUI in my VM console window?

Yes, you can!

First, log in to the VM in its console window (i.e. type directly into the VM window without using `ssh`) and make sure `apt` is up to date:

    sudo apt-get update

Then, install the desktop environment of your choice.
     
    sudo apt-get install xinit <environment>

where `<environment>` is your GUI environment of choice. Some options:

* `lxde`: a reasonably compact and and fast desktop environment
* `flwm`: a smaller but more primitive desktop environment
* `ubuntu-desktop`: the full, heavyweight Ubuntu Unity desktop environment

Then, you can start X11 in the VM console window using

    startx

***
<a id=vm-gui-mininethost></a>
### Can I run a GUI/X11 application within a Mininet host?

Yes, you can do so from a host `xterm` or `gterm` with the current version of Mininet.

This allows you to run programs like `wireshark` or `firefox`. You might want to `su` to another user (e.g. `mininet`) to avoid running FireFox with `root` privileges, but it probably doesn't make a fundamental difference in a Mininet VM that is configured for password-less `sudo`.

**What about Mininet 2.0.0?**

(Thanks to Murphy McCauley for providing the following workaround, which craftily uses the switch's CPU port!)

This is a bit of a hack at this moment but it works! 

Using a graphical browser requires that you get X11 traffic out of your Mininet host namespace and into the environment where you actually have an X display.

Say the X display is the host environment with IP address of 192.168.56.1 and the Mininet VM has an IP of 192.168.56.101.

In short, run sshd inside Mininet's h1. Then SSH from the host environment (192.168.56.1) to the Mininet VM (192.168.56.101) with X forwarding (ssh -Y mininet@192.168.56.101). Then SSH from the Mininet VM into h1 with X forwarding (again!).

As an example, open three terminals in the host environment (Term1, Term2, Term3)

On Term1:

    ./pox.py forwarding.l2_learning # Run an OpenFlow controller

On Term2:

    ssh -Y mininet@192.168.56.101 # SSH into the Mininet VM with X forwarding
    sudo mn --topo=linear,2 --mac --controller=remote,ip=192.168.56.1:6633
    h1 /usr/sbin/sshd # From the mininet> prompt, run sshd inside the h1 namespace

On Term3:

    ssh -Y mininet@192.168.56.101 # SSH into the Mininet VM with X forwarding
    sudo ifconfig s1 10.12.12.12 # Give the internal adapter for s1 an address #By default, all hosts live on 10.0.0.0/8 space. 
    ssh -Y mininet@10.0.0.1 # SSH into the Mininet h1 namespace with X forwarding
    xeyes # Run any X app

***
<a id=run></a>
### How do I run Linux programs on my Mininet hosts?

If you are asking this question, it means you haven't yet consulted the [[Documentation]]. :(

If you are incredibly lazy, please at least look at the [[Sample Workflow | http://mininet.org/sample-workflow/ ]].

***
<a id=command-line-options></a>
### How do I figure out the **command-line options** for the `mn` command?

    mn --help

***
<a id=native-install></a>
### How can I do a native install of Mininet?

Instructions for native installation can be found at <http://mininet.github.com/download> and in [`INSTALL`](https://github.com/mininet/mininet/tree/master/INSTALL).

***
<a id=virtualbox-error></a>
### Help! I am getting an error from VirtualBox and my VM won't start!

If you are already running Microsoft's Hyper-V, you may not be able to boot the Mininet VM in VirtualBox at the same time. I tested this and got the following error:

> VirtualBox - Error
> VT-X/AMD-V Hardware acceleration is not available on your system. Your 64-bit guest will fail
> to detect a 64-bit CPU and will not be able to boot.

The problem is not that you don't have a 64-bit CPU (you probably do if you have anything as good as, say an intel Core 2 Duo from 2006.) Rather, the problem is that you are trying to run two virtual machine monitors - Hyper-V and VirtualBox - at the same time, and this does not work with 64-bit guest OSes.

A simple solution to this problem is to turn off Hyper-V (in Windows 8, this is done via "Enable/Disable Windows Features" in the Windows Control Panel.) This has been tested and verified on Windows 8.

If you need to keep running Hyper-V, you could try creating a 32-bit Mininet VM (very easy - just read our instructions on the Documentation page), or you could run the Mininet VM image natively in Hyper-V by:

1. Converting the `.vmdk` disk image to a `.vhd` using Microsoft's [Virtual Machine Converter](http://technet.microsoft.com/en-us/library/hh967435.aspx).
2. Creating a new Hyper-V virtual machine using the new `.vhd` image as its hard drive.
3. Creating an "external" virtual switch in Hyper-V manager, sharing the interface with the host OS.


***
<a id=blank-screen></a>
### Help! The VM console screen is blank!

Make sure that the VM is actually booting. If not, then you may have a problem actually booting the Mininet VM in your VM monitor. The Mininet 2.0 VM is a 64-bit image, which should work on any modern CPU. However, some users have reported conflicts between Microsoft's Hyper-V and VirtualBox on Windows, so if you are running VirtualBox on Windows you may need to turn of Hyper-V.

If the VM is booting but boots to a blank screen, then you probably just need to either wake up the Linux console or switch to another virtual console, as follows:

First, select the VM console window.
Second, press a key like `A` or `return` a few times - see if any text appears.
If nothing happens, try switching to a different Linux console using `control``alt``F1`` through `control``alt``F7`.

Note: On a Mac laptop using VMware Fusion, you may need to type `fn``control``option``F1`, since the `F1` key controls brightness by default.

***
<a id=python-api></a>
### How do I **use Mininet's Python API**?

Check out the [[Introduction to Mininet]] for an introduction to Mininet and its Python API.

Several useful examples of using the Python API can also be found in the [`mininet/examples`](https://github.com/mininet/mininet/tree/master/examples) directory.

We also provide Python DocStrings for every Mininet class and method, and you can view them using from within Python

    >>> import mininet.node
    >>> help(mininet.node.Node)

or by calling Python from within the Mininet CLI:

    mininet> py help(h2)

In each case, pressing `q` should quit the pager.

The API documentation is available at <http://mininet.github.com/api>.

Also see [[Mininet API Documentation]] for information on how to generate Mininet documentation yourself in .html and .pdf format.

***
<a id=NAT></a>

### Why can't I **ping Google** from my Mininet hosts?

You can't ping `google.com` because your Mininet network is not connected to the internet. This is usually a good thing!  Usually Mininet networks use a non-routable IP address range like `10.0.0.0/8`. 

However, you can set up NAT if you like.

### How can I set up NAT?

The illustrious Glen Gibb provided a script to do it back in 2011 on 
[[ mininet-discuss | https://mailman.stanford.edu/pipermail/mininet-discuss/2011-February/000289.html ]].
There was also a [[ follow-up message | https://mailman.stanford.edu/pipermail/mininet-discuss/2013-March/001821.html ]] from Leo Alterman.

Another updated version of the script can be found here: [[Mininet NAT Script | https://gist.github.com/lantz/5640610 ]].

Note that:

* The script assumes that `eth0` is the host interface connected to the internet/your LAN.
  You may need to change it if this is not the case!

* The script adds the following line to `/etc/network/interfaces`:

        iface root-eth0 inet manual

If this script does not work for you, please make an effort to debug and fix the problem, and then update this FAQ entry.

***
<a id=openflow-versions></a>
### Which **versions of OpenFlow** does Mininet support?

Mininet officially supports OpenFlow 1.0, and the Mininet VM image includes OpenFlow 1.0-compatible versions of the OpenFlow reference implementation as well as Open vSwitch. It is also possible to get Mininet to run with OpenFlow 0.8.9 with a few minor changes. Recently, install.sh was updated to optionally install the CPqD version of the OpenFlow reference implementation and NOX classic, both of which support OpenFlow 1.3. Also, it is rumored that recent versions of Open vSwitch have partial support for OpenFlow 1.3, so you may be able to get these versions to run with Mininet as well.

***
<a id=openflow-switches></a>
### What **OpenFlow switch implementations** does Mininet support?

Mininet currently includes support for the user space reference implementations, as well as the Open vSwitch kernel implementation. All three are included in the VM image. Mininet used to support the OpenFlow 0.8.9 kernel reference implementation (`--switch kernel`) but that is now obsolete and has largely been replaced with Open vSwitch.

The command line options are `--switch user` and `--switch ovsk` for the user reference and Open vSwitch kernel switches, respectively.

***
<a id=ethernet-loops></a>
### Why does my controller, which implements an Ethernet bridge or learning switch, not work with my network which has loops in it? I can't ping anything!

It doesn't work because your network has loops in it.

Transparent bridging of L2/Ethernet networks doesn't work if the topology has loops in it, for a variety of reasons: ARP does broadcasts, packets are flooded by default, learning switches don't deal well with seeing the same MAC address on multiple ports and could potentially learn a route to themselves, and Ethernet frames don't have a time to live field (TTL) like IP packets (otherwise flooding might work, if inefficiently.) As a result, many Ethernet bridges implement variants of a Spanning Tree Protocol (STP), which simply deactivates links in the network to remove loops. Of course, this also throws away network bandwidth that you could otherwise be using, and creates a bottleneck at the root of the tree.

The OpenFlow reference controller (`controller`) implements a bridge/learning switch, as does `ovs-controller` (currently the default controller for Mininet), as does NOX's `pyswitch` module, and they don't implement a spanning-tree protocol by default. As a result, they **will not work with a network that has loops in it**.

In general, if you want to use a network with loops in it, you need to be absolutely sure that your controller supports such a network. As mentioned above, `ovs-controller`, `controller` and `pyswitch` **do not** by default. NOX classic and POX include spanning tree modules, which you may wish to investigate.

"That sounds too much like work - I don't want to do any work!"

You may also wish to take a look at [RipL-POX](https://github.com/brandonheller/riplpox), which provides starter code for a multipath-capable controller, as well as some of the multipath experiments on http://reproducingnetworkresearch.blogspot.com. But, you will still probably have to do some work and actually understand what you are doing.

***
<a id=assign-macs></a>
### Why do the switch data ports have random MAC addresses? How do I assign MAC and IP addresses to the switch data ports?

The MAC addresses reported by Linux for the switch data ports are meaningless. The switch is controlled by OpenFlow, so you should use OpenFlow to ensure that any packets destined "for the switch" are properly routed. You "assign" MAC and IP addresses "to the switch" by using OpenFlow rather than the Linux IP stack. If you attempt to use the Linux IP stack instead, it really won't work unless you are using the Linux kernel for routing (which you aren't - you're using an OpenFlow switch!) You should never attempt to use `ifconfig` or `ip link` or other utilities to assign an IP address to a switch data port. Usually you will want your controller to handle packets such as ARP and ICMP which are sent to and from "the switch," and you will want IP packets which are sent to to be handled by appropriate flow table entries. You can pick any "MAC" address you like for the switch.

<a id =ovs-controller></a>
### Why does my network fail if I use more than 16 switches?

For Mininet 2.0.0, the default controller for the `mn` command  is `ovs-controller` (which can be installed automatically in Ubuntu.) Unfortunately `ovs-controller` only supports up to 16 switches. If you want to use more than 16 switches, you should use a controller that supports more than 16 switches, for example:

    sudo mn --controller ref --topo linear,20 --test pingall

Make sure you've installed the Mininet version of the OpenFlow reference controller, which is easily done using:

    mininet/util/install.sh -f

You can also create a custom controller class or use `--controller external:IP` and use any custom or off-the-shelf controller that you like. For example, ou can easily install POX by checking it out or using `util/install.sh -p`, and you can install Floodlight on Ubuntu using `apt-get install floodlight`.

*** 
<a id=remote-control></a>

### How can I control my Mininet hosts remotely?

It's trivial to control Mininet hosts from the CLI or from within a Python script running locally, but what if you want some other process or even another computer on your LAN to be able to control your Mininet network remotely?

Well, there are lots of ways to do this. One idea is that anything you can do in Python, you can do in Mininet, and it's often very easy to do so. For example, there are all sorts of frameworks available for any kind of messaging you can imagine. (See below for a REST example - it's just a few lines of code.)

Another easy way to control Mininet hosts in the current `master` branch is to use the `util/m` script.

For example if my Mininet server is `ubuntu1`, I can run `ifconfig` on host `h1` using

    $ ssh ubuntu1 mininet/util/m h1 ifconfig

Another way is to actually connect your Mininet network to your LAN and to run `sshd` on your Mininet hosts. This is left as an exercise for the reader, but you may want to look at the `hwintf.py` and `sshd.py` scripts in `examples/` to understand how you might possibly do this.

***
<a id=rest></a>

### How can I add a REST interface to Mininet?

Basically anything you can do in Python you can do in Mininet, and it's often very easy to do so. For example, there are all sorts of frameworks available for various kinds of messaging and RPC, REST, JSON, SOAP, XML, etc.. You can hook Python code up to node.js, you can have it speak ZeroMQ, you can use Apache Thrift... really the possibilities are endless!!

Note however that if you are running locally it's much easier to control Mininet directly from within a Python script or using the CLI.

It's trivial to add a REST (or ReST if you prefer) API to Mininet using Python. For example, using the [[ Bottle | http://bottlepy.org ]] framework, you could do something like:

```python
#!/usr/bin/python

from mininet.net import Mininet
from mininet.topo import SingleSwitchTopo

from bottle import route, run, template

net = Mininet( topo=SingleSwitchTopo( 2 ) )

@route('/cmd/<node>/<cmd>')
def cmd( node='h1', cmd='hostname' ):
    out, err, code = net.get( node ).pexec( cmd )
    return out + err

@route('/stop')
def stop():
    net.stop()

run(host='localhost', port=8080 )
```

This allows you to send simple commands to your Mininet hosts. 

*Note that `host.pexec()` (like `host.cmd()`) runs commands as root, so this isn't really something you want to expose to the whole internet (though you could firewall port 8080 on your Mininet server and then use `ssh` for a secure connection.) But it's quite convenient, isn't it? In a real example you would probably want a method to shut down both the REST server and the Mininet network in a graceful manner rather than using control-C and `mn -c`.]*

After running this script in one window:

    $ sudo ./rest.py

You can easily try it out. For example, you could run the `ifconfig h1-eth0` command on host `h1` as follows:

```
$ curl localhost:8080/cmd/h1/ifconfig%20h1-eth0
h1-eth0   Link encap:Ethernet  HWaddr 36:6f:c0:28:a3:f9  
          inet addr:10.0.0.1  Bcast:10.255.255.255  Mask:255.0.0.0
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:4 errors:0 dropped:0 overruns:0 frame:0
          TX packets:3 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:328 (328.0 B)  TX bytes:238 (238.0 B)
```
Note that you need to escape the space in `ifconfig h1-eth0` as `%20` as you would normally in a URL.

***
<a id=examples></a>
### How do I run the **Mininet examples**?

1. Fetch the Mininet source tree if you haven't already

        git clone http://mininet.github.com/mininet/mininet

2. Run an example

        sudo mininet/examples/treeping64.py

Note some examples (`consoles.py`, `miniedit.py`) require you to have set up
[X11 forwarding](#x11-forwarding). Others require that you have installed the
OpenFlow reference implementation using `mininet/util/install.sh -f`. Consult
the comments in the example's `.py` file for details.

***
<a id=dpctl></a>

### Why doesn't `dpctl` work? How can I dump a switch's flow table?

`dpctl` should work fine with the Stanford OpenFlow reference implementation or the CPqD version of same.


If you're running Open vSwitch, or need to open up a listening port on either OVS or the reference switch so that you can connect to a port, read on...

1. If you are using Open vSwitch, the correct command to use from the shell prompt is

        $ sudo ovs-ofctl dump-flows s1

    (If you try to use `ovs-dpctl` with Open vSwitch, you will only see the cached flows
    in the kernel, rather than the switch's full flow table.)

2. The correct command to use from the Mininet prompt is

        mininet> dpctl dump-flows

    which dumps all the flows on all switches, and works for both OVS and the reference switch.

    If you're running OVS, you can pass the switch name to `ovs-ofctl` and it will connect
    to it via the file system:

        mininet> sh ovs-ofctl dump-flows s1

     or, from the shell prompt:

        $ ovs-ofctl dump-flows s1

3. If you want to open up a listening port on the switch, you need to specify the base listening port, e.g.

        net = Mininet( topo=topo, listenPort=6634 )

    Ports will be allocated sequentially starting with the value you specify.

    Note if you want to dump the flows from the reference switch, you will need to have a 
    listening port opened up; then you can use `dpctl`:


        $ dpctl dump-flows tcp:localhost:6634

    Note that `ovs-ofctl` doesn't like `localhost`, so you should use 127.0.0.1:

        $ ovs-ofctl dump-flows tcp:127.0.0.1:6634



***
<a id=multiple-controllers></a>
### How can I use **multiple controllers** in my network?

One way is to make a custom switch class, as shown in [`examples/controllers.py`](https://github.com/mininet/mininet/tree/master/examples/controllers.py)

Another way is to use the mid-level API, as shown in [`examples/controllers2.py`]
(https://github.com/mininet/mininet/tree/master/examples/controllers2.py)

***
<a id=ovs-reconfig></a>
### Help! I updated my Ubuntu kernel and now Open vSwitch won't start!

If you are using Ubuntu's `openvswitch-datapath-dkms` and `openvswitch-switch` packages, they should update automatically when you reboot.

Until `openvswitch-datapath-dkms` is reconfigured/rebuilt, Open vSwitch will refuse to start saying that its kernel module is missing. For example, you may see a message like:

    FATAL: Module openvswitch not found.

If rebooting doesn't fix the problem, or if you don't want to reboot, you can reconfigure the kernel module manually and restart OVS:

    sudo dpkg-reconfigure openvswitch-datapath-dkms
    sudo service openvswitch-switch restart


***
<a id=openflow></a>
### Help! **I don't understand OpenFlow** or SDN!

There is a wealth of useful information to be found at <http://openflow.org>.

Definitely read the [OpenFlow White Paper](http://www.openflow.org/documents/openflow-wp-latest.pdf) and go through the [OpenFlow Tutorial](http://www.openflow.org/wk/index.php/OpenFlow_Tutorial).

You may also wish to search the ACM and IEEE digital libraries for recent papers that reference Software-Defined Networking and OpenFlow.

***
<a id=networking></a>
### Help! **I don't understand networking** (or maybe computers) at all!!

There is a wealth of very useful information available for free on the internet - try some Google (or search engine of your choice) searches for things like 'networking tutorial', 'IP networking basics', etc..

You may also find useful content on Wikipedia, About.com and YouTube, as well as free online courses.

My [BL's] personal recommendation is to take an introductory CS course and an introductory networking course at your local college or university.

You may also wish to consult an introductory networking textbook such as *Computer Networks: A Top-Down Approach* by Kurose and Ross, or *Computer Networks: A Systems Approach* by Peterson and Davie. To master Ethernet switches you might want to check out *The All-New Switch Book* by Seifert, and for a vintage but still classic and somewhat relevant view of socket programming you might find a copy of the *Unix Network Programming* tomes by the late Richard Stevens.

***
<a id=linux></a>
### Help! **I have never used Linux or Unix before!**

<a id=man></a>
### How do I use (some Linux command)?

There is a wide variety of very useful information available for free on the internet. Try searching for "Linux tutorial" or "Ubuntu tutorial" in Google or your search engine of choice.

Additionally, nearly every Unix system since the beginning of time includes online documentation which can be accessed using the `man` command. For example, to find out about the `ls` command, you can type

    man ls

Each section (traditionally 1-8) of the manual has an `intro` page, and you can actually read *all* of the intro sections by typing:

    man -a intro

Some GNU software hides its documentation in the (powerful but less friendly) `info` documentation system. Usually there is a `man` page which will direct you to it.

Additionally, `bash` has a `help` command which can be used to find out how to use shell commands.

There are also many useful books on Linux available on Amazon and in your local bookstore. Mark Sobell's books are classics, as are Nemeth's books on Unix/Linux system administration.

***
<a id=homework></a>
### Will you do my (home)work assignment (or paper/thesis/project/etc.) for me? It's due next Tuesday at 4pm.

No, because:

* We don't want to and don't have time
* You don't have enough money to pay our consulting rates anyway
* It would be a violation of the honor code
* You will learn much more doing the assignment yourself
* You really want to learn how to use [[Google scholar | http://scholar.google.com]],
[[citeseer | http://citeseerx.ist.psu.edu]], various digital libraries from the 
[[ACM | http://dl.acm.org]], [[IEEE | http://ieeexplore.ieee.org]], and [[USENIX | http://static.usenix.org/publications/library/]], 
and also learn [[how to do a literature search | http://blizzard.cs.uwaterloo.ca/keshav/home/Papers/data/07/paper-reading.pdf]]
* It's the job of your advisor/TAs/school/company/self to teach/train you, so make them do their job!
* Learning how to teach yourself is even more valuable than learning from someone else.


***