<!-- %META:TOPICINFO{author="BobLantz" date="1339625148" format="1.1" reprev="1.6" version="1.6"}% -->
<!-- %META:TOPICPARENT{name="Mininet"}% -->
<!-- Use our custom page layout:
* Set VIEW_TEMPLATE = [MininetView](MininetView)
-->


The Mininet Approach to Network Research and Prototyping
---------------------------------------------------------

Mininet was conceived as a way to make it easy to do network research using !OpenFlow.

It is based on several guiding ideas and principles:

* It should be easy and fun to create !OpenFlow networks / software-defined networks
* Individual researchers and students should be able to easily create and experiment with networks on a single laptop
* Current laptops should be able to create very large experimental networks (with 1990s-class performance ;-) )
* Current clusters should be able to simulate enterprise-class (e.g. 25,000 nodes or more) networks
* Network topologies should be easy to create by specifying simple _parameters_
* Python is (or should be!) simpler than XML for specifying network configurations
* A single network console that can control an entire network is better than hundreds of <code>xterm</code> or <code>ssh</code> sessions
* Full virtualization is overkill for most applications
* Network systems and experiments should be easy to share, download, and reproduce
* Network designs should move seamlessly from emulation to running at full speed/line rate on hardware
Although Mininet was designed for !OpenFlow networks, it also supports legacy IP networking, e.g. using Open vSwitch or an !OpenFlow controller that supports standard Ethernet switching and IP routing.


### A Network in a Laptop: Rapid Prototyping for Software Defined Networks

Brandon's presentation from Hotnets-IX (10/20/2010) explains the Mininet architecture and SDN workflow, from prototyping to hardware deployment, and includes a slide on adding performance fidelity to Mininet.

* [A Network in a Laptop: Rapid Prototyping for Software Defined Networks](%ATTACHURL%/mininet-hotnets2010-final.pdf)

### Mininet: Squeezing a 1000-node Openflow Network onto a Laptop

Here is my original presentation to the !OpenFlow Software Architecture and Implementation (SWAI) working group from 11/19/2009 (with an additional slide to illustrate the kernel datapath):

* ["Mininet: Squeezing a 1000-node [OpenFlow](OpenFlow) Network onto a Laptop"](%ATTACHURL%/mininet-presentation-2009.pdf)
-- [Main/BobLantz](../Main/BobLantz) - 11 Feb 2010

- - -+++

<!-- %META:FILEATTACHMENT{name="mininet-presentation-2009.pdf" attachment="mininet-presentation-2009.pdf" attr="" comment="Original Mininet presentation from 11/19/2009 (with added slide for kernel datapath)" date="1265849535" path="mininet-presentation-2009.pdf" size="1683011" stream="IO::File=GLOB(0xbec4638)" tmpFilename="/usr/tmp/CGItemp21831" user="BobLantz" version="1"}% -->
<!-- %META:FILEATTACHMENT{name="mininet-hotnets2010-final.pdf" attachment="mininet-hotnets2010-final.pdf" attr="" comment="Mininet presentation from [HotNets](HotNets)-IX, 2010." date="1339624410" path="mininet-hotnets2010-final.pdf" size="3088233" stream="IO::File=GLOB(0x9cd3c34)" tmpFilename="/usr/tmp/CGItemp20903" user="BobLantz" version="1"}% -->
