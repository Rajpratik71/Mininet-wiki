Please add helpful **Frequently Asked Questions (FAQs)** and **high-quality answers (HQAs)** below.

*Since github wikis don't automatically generate a Table of Contents, please follow the existing format and add 1) an anchor for your question and 2) a link to it at the top of the page. Thanks!*

<!-- this doesn't work yet, alas :(
[[_TOC_]]
-->

* [How can I **get started** with Mininet?](#get-started)
* [How do I figure out the **command-line options** for the `mn` command?](#command-line-options)
* [How can I do a **native install** of Mininet?](#native-install)
* [How do I **use Mininet's Python API**?](#python-api)
* [Which **versions of OpenFlow** does Mininet support?](#openflow-versions)
* [What **OpenFlow switch implementations** does Mininet support?](#openflow-switches)
* [Why does my controller, which implements an **Ethernet bridge or learning switch**, not work with my network which has **loops** in it? I can't ping anything!](#ethernet-loops)
* [Why do the **switch data ports** have **random MAC addresses**? How do I assign MAC and IP addresses to the switch data ports?](#assign-macs)
* [Why can't I get **X11 forwarding** to work? I get "cannot open display:" or "$DISPLAY not set" and WireShark doesn't work! `xterm` doesn't work either!](#x11-forwarding)

<a id=get-started>***</a>
### How can I **get started** with Mininet?

The best way to get started with Mininet is to install our ready-to-run virtual machine image from the [Download](Download) page, and then go through the [Walkthrough](Walkthrough.md) and the [[OpenFlow tutorial | http://www.openflow.org/wk/index.php/OpenFlow_Tutorial]].

<a id=command-line-options></a>
### How do I figure out the **command-line options** for the `mn` command?

    mn --help

***
<a id=native-install></a>
### How can I do a native install of Mininet?

It's probably easiest to start out with a [VM install](Download) of Mininet - it's pretty much guaranteed to work!

Instructions for native installation can be found [here](Download) as well.

***
<a id=python-api></a>
### How do I **use Mininet's Python API**?

We provide a few examples of using the Python API in the `mininet/examples` directory.

We also provide Python DocStrings for every Mininet class and method, and you can view them using from within Python

    >>> import mininet.node
    >>> help(mininet.node.Node)</verbatim>

or by calling Python from within the Mininet CLI:

    mininet> py help(h2)

In each case, pressing =q= should quit the pager.

Also see [Mininet-API-Documentation](MininetAPIDocumentation) for information on how to generate Mininet documentation in.md format.

***
<a id=openflow-versions></a>
### Which **versions of OpenFlow** does Mininet support?

Mininet officially supports OpenFlow 1.0, and the Mininet VM image includes OpenFlow 1.0-compatible versions of the OpenFlow reference implementation as well as Open vSwitch. It is also possible to get Mininet to run with OpenFlow 0.8.9 with a few minor changes.

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

The OpenFlow reference controller (`controller`) implements a bridge/learning switch, as does NOX's `pyswitch` module.

In general, if you want to use a network with loops in it, you need to be absolutely sure that your controller supports such a network. As mentioned above, `controller` and `pyswitch` do not by default. NOX has a `spanning_tree` module, which you may wish to investigate.

***
<a id=assign-macs></a>
### Why do the switch data ports have random MAC addresses? How do I assign MAC and IP addresses to the switch data ports?

The MAC addresses reported by Linux for the switch data ports are meaningless. The switch is controlled by OpenFlow, so you should use OpenFlow to ensure that any packets destined "for the switch" are properly routed. You "assign" MAC and IP addresses "to the switch" by using OpenFlow rather than the Linux IP stack. If you attempt to use the Linux IP stack instead, it really won't work unless you are using the Linux kernel for routing (which you aren't - you're using an OpenFlow switch!) You should never attempt to use `ifconfig` or `ip link` or other utilities to assign an IP address to a switch data port. Usually you will want your controller to handle packets such as ARP and ICMP which are sent to and from "the switch," and you will want IP packets which are sent to to be handled by appropriate flow table entries. You can pick any "MAC" address you like for the switch.

***
<a id=x11-forwarding></a>
### Why can't I get X11 forwarding to work? I get "cannot open display:" or "$DISPLAY not set" and WireShark doesn't work! `xterm` doesn't work either!

This is not a Mininet problem. It means that X11 forwarding is not set up correctly.
First, consult the X11 setup instructions in the [http://www.openflow.org/wk/index.php/OpenFlow_Tutorial](OpenFlow Tutorial), including:

* Download X11
* Install X11
* Start up X11 server
* Access VM via `ssh`

Make sure you have carefully followed the necessary steps.
If things are still not working for you, you will want to make sure that:

Your X11 server on your client machine (e.g. your laptop) is installed correctly and is actually running
You are connecting using X11 forwarding (e.g. `ssh -X` on OS X/Linux or enabling X11 forwarding in a Windows ssh client like PuTTY or SecureCRT.)

You don't have any options in your client `.ssh/config` which interfere with X11 forwarding

When you log in with `ssh`, your `$DISPLAY` environment variable is set
X11 terminology is a bit confusing because the X11 server is actually run on the ssh client machine! The ssh client connects to the sshd server, which in turn forwards connections from X11 client applications (such as wireshark) to the local X11 server (usually running on your laptop or whatever machine is sitting in front of you.)

One note: if you have disabled IPv6, you may find that you need to add `AddressFamily inet` to your `/etc/ssh/sshd_config`.

Other unlikely causes of breaking X11 forwarding include it being disabled in `/etc/sshd_config` or disabled by SELinux. Neither of these should be the case in the Mininet VM image we provide.
You may wish to invoke debug logging on your ssh client to see why X11 forwarding isn't working. On OS X and Linux, you can use

    ssh -X -v <VM's IP address>

to see where the X11 forwarding is failing.

If this seems too complicated, you can simply run X11 in the VM console window
First, log in to the VM in its console window (i.e. type directly into the VM window without using `ssh`.) Then, install `xinit` (and its dependencies) and a window manager of your choice (I picked `flwm` because it's small - you may prefer `metacity` or some other window manager):

    sudo apt-get install xinit flwm

Then, you can start X11 in the VM console window using

    startx

As an alternative to X11, you could also use VNC, but that is probably about as complicated as X11 and is left as an exercise to the reader.