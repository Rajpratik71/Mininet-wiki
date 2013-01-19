##Important Links
* [PWOSPF specification](http://yuba.stanford.edu/cs344/admin/pwospf/)
* [Assignment FAQ](http://yuba.stanford.edu/vns/?page_id=75)
* [Modified stub code v0.23](http://yuba.stanford.edu/vns/files/assignments/pwospf/stub_pwospf-0.23.tar.gz) (last updated 2010-Nov-03) (it is not required that you use this, but highly recommended!)
* [Using your sr router with the PWOSPF assignment](http://yuba.stanford.edu/vns/assignments/pwospf/adding-ospf-functionality-to-your-sr-router/)

##Introduction
This assignment involves building advanced functionality on top of a basic VNS router. The goal is to develop a simple dynamic routing protocol, PWOSPF, so that your router can generate its forwarding table automatically based on routes advertised by other routers on the network. By the end of this project, your router is expected to be able to build its forwarding table from link-state advertisements sent from other routers, and route traffic through complex topologies containing multiple nodes.

##PWOSPF
The routing protocol you will be implementing is a link state protocol that is loosely based on OSPFv2. You may find a full specification of PWOSPF [here](http://yuba.stanford.edu/cs344/pwospf/). Note that while PWOSPF is based on OSPFv2 it is sufficiently different that referring to the OSPFv2 as a reference will not be of much help and contrarily may confuse or mislead you.

##Topology and Requirements Overview

Each team will be assigned the following three host topology for development:

![alt text](http://yuba.stanford.edu/~huangty/mininet/pwospf_topo.png "Topology for PWOSPF")
 
