<!-- %META:TOPICINFO{author="BobLantz" date="1341340694" format="1.1" reprev="1.10" version="1.10"}% -->
<!-- %META:TOPICPARENT{name="Mininet"}% -->
<!-- Use
 our custom page layout:
* Set VIEW_TEMPLATE = [MininetView](MininetView)
-->

Mininet FAQ
------------

Please add helpful, Frequently Asked Questions and high-quality answers below.

<!-- %TOC% -->


### How can I **get started** with Mininet?

The best way to get started with Mininet is to install our ready-to-run virtual machine image from the [MininetGettingStarted](MininetGettingStarted) page, and then go through the [MininetWalkthrough](MininetWalkthrough.md) and the [OpenFlow tutorial](http://www.openflow.org/wk/index.php/OpenFlow_Tutorial).


### How do I figure out the **command-line options** for the <code>mn</code> command?

<verbatim>mn --help</verbatim>


### I get an error every time I try to **edit a wiki page** - help!

Unfortunately you can't currently edit the wiki in <code>https</code> mode. Change the URL of the page from <code>https</code> to <code>http</code> and try again.


### How can I do a native install of Mininet?

It's probably easiest to start out with a [VM install](MininetGettingStarted) of Mininet - it's pretty much guaranteed to work!

Instructions for native installation can be found [here](MininetGettingStarted) as well.


### How do I **use Mininet's Python API**?

We provide a few examples of using the Python API in the <code>mininet/examples</code> directory.

We also provide Python !DocStrings for every Mininet class and method, and you can view them using from within Python

<verbatim>
>>> import mininet.node
>>> help(mininet.node.Node)</verbatim>

or by calling Python from within the Mininet CLI:

<verbatim>
mininet> py help(h2)</verbatim>

In each case, pressing =q= should quit the pager.

Also see [MininetAPIDocumentation](MininetAPIDocumentation) for information on how to generate Mininet documentation in.md format.


### Which **versions of !OpenFlow** does Mininet support?

Mininet officially supports !OpenFlow 1.0, and the Mininet VM image includes !OpenFlow 1.0-compatible versions of the !OpenFlow reference implementation as well as Open vSwitch. It is also possible to get Mininet to run with !OpenFlow 0.8.9 with a few minor changes.


### What **OpenFlow switch implementations** does Mininet support?

Mininet currently includes support for the user space reference implementations, as well as the Open vSwitch kernel implementation. All three are included in the VM image. Mininet used to support the !OpenFlow 0.8.9 kernel reference implementation (--switch kernel) but that is now obsolete and has largely been replaced with Open vSwitch.

The command line options are <code>--switch user</code> and <code>--switch ovsk</code> for the user reference and Open vSwitch kernel switches, respectively.


### Why does my controller, which implements an Ethernet bridge or learning switch, not work with my network which has loops in it? I can't ping anything!

It doesn't work because your network has loops in it.

Transparent bridging of L2/Ethernet networks doesn't work if the topology has loops in it, for a variety of reasons: ARP does broadcasts, packets are flooded by default, learning switches don't deal well with seeing the same MAC address on multiple ports and could potentially learn a route to themselves, and Ethernet frames don't have a time to live field (TTL) like IP packets (otherwise flooding might work, if inefficiently.) As a result, many Ethernet bridges implement variants of a Spanning Tree Protocol (STP), which simply deactivates links in the network to remove loops. Of course, this also throws away network bandwidth that you could otherwise be using, and creates a bottleneck at the root of the tree.

The !OpenFlow reference controller ( <code>controller</code>) implements a bridge/learning switch, as does NOX's <code>pyswitch</code> module.

In general, if you want to use a network with loops in it, you need to be absolutely sure that your controller supports such a network. As mentioned above, <code>controller</code> and <code>pyswitch</code> do not by default. NOX has a <code>spanning_tree</code> module, which you may wish to investigate.


### Why do the switch data ports have random MAC addresses? How do I assign MAC and IP addresses to the switch data ports?

The MAC addresses reported by Linux for the switch data ports are meaningless. The switch is controlled by !OpenFlow, so you should use !OpenFlow to ensure that any packets destined "for the switch" are properly routed. You "assign" MAC and IP addresses "to the switch" by using !OpenFlow rather than the Linux IP stack. If you attempt to use the Linux IP stack instead, it really won't work unless you are using the Linux kernel for routing (which you aren't - you're using an !OpenFlow switch!) You should never attempt to use "ifconfig" or other utilities to assign an IP address to a switch data port. Usually you will want your controller to handle packets such as ARP and ICMP which are sent to and from "the switch," and you will want IP packets which are sent to to be handled by appropriate flow table entries. You can pick any "MAC" address you like for the switch.
