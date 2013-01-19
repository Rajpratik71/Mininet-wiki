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

The topology of PWOSPF is as follows. There are three routers inter-connected with each other. Client connects to the network through vhost1; Server1 connects to the network through vhost2; Server2 connects to the network through vhost3.

![alt text](http://yuba.stanford.edu/~huangty/mininet/pwospf_topo.png "Topology for PWOSPF")

Your task is to implement PWOSPF within your router so that your router will be able to do the following:

* Build the correct forwarding tables on the assignment topology
* Detect when routers join/or leave the topology and correct the forwarding tables correctly
* Inter-operate with a third party reference solution that implements pwosp

## Implementation Guidance
To get you started on the right track, we provide skeleton code for a possible data structure. You will use the same environment that we setup in "simple router" for this assignment. For detailed instructions, please refer to [[Environment Setup]] and [[Simple Router]].

Note: **Make sure your EC2 Machine's Security Group opens ports 8888 and 2300. Otherwise, your routers will not be able to communicate with each other.** 

### Download Skeleton Code
Boot up your machine setup with Mininet. Open up a terminal on the machine, download the skeleton code for "PWOSPF" using git.
```no-highlight
> cd ~
> git clone https://huangty@bitbucket.org/huangty/pwospf.git
> cd pwospf
```

The skeleton code resides in pwospf_stub/. If you have implemented "Simple Router", you should make a copy of your "Simple Router" into the pwospf/ directory as follows:

```no-highlight
> pwd
/home/ubuntu/pwospf
> rm -rf pwospf_stub
> cp -r ~/cs144_lab3/router ./pwospf_stub
```
### Test Connectivity of Your Emulated Topology
We also provide a reference implementation (binary) for you to test the environment. 
#### Configure the Environment
First, configure the environment by running the following command.
```no-highlight
> ./config.sh
```
#### Start Mininet
```no-highlight
> ./run_mininet.sh
```
You should see an output that looks like this (except for the IP addresses).
```
*** Shutting down stale SimpleHTTPServers  
*** Shutting down stale webservers  
server1 192.168.2.200
server2 172.24.3.30
client 10.0.1.100
vhost1-eth1 10.0.1.1
vhost1-eth2 10.0.2.1
vhost1-eth3 10.0.3.1
vhost2-eth1 10.0.2.2
vhost2-eth2 192.168.2.2
vhost2-eth3 192.168.3.1
vhost3-eth1 10.0.3.2
vhost3-eth2 172.24.3.2
vhost3-eth3 192.168.3.2
*** Successfully loaded ip settings for hosts
 {'vhost1-eth2': '10.0.2.1', 'vhost1-eth3': '10.0.3.1', 'server2': '172.24.3.30', 'server1': '192.168.2.200', 'vhost3-eth3': '192.168.3.2', 'vhost3-eth1': '10.0.3.2', 'vhost3-eth2': '172.24.3.2', 'client': '10.0.1.100', 'vhost2-eth3': '192.168.3.1', 'vhost2-eth2': '192.168.2.2', 'vhost2-eth1': '10.0.2.2', 'vhost1-eth1': '10.0.1.1'}
*** Creating network
*** Creating network
*** Adding controller
*** Adding hosts:
client server1 server2 
*** Adding switches:
vhost1 vhost2 vhost3 
*** Adding links:
(client, vhost1) (server1, vhost2) (server2, vhost3) (vhost1, vhost2) (vhost1, vhost3) (vhost2, vhost3) 
*** Configuring hosts
client server1 server2 
*** Starting controller
*** Starting 3 switches
vhost1 vhost2 vhost3 
*** setting default gateway of host server1
server1 192.168.2.2
*** setting default gateway of host server2
server2 172.24.3.2
*** setting default gateway of host client
client 10.0.1.1
*** Starting SimpleHTTPServer on host server1 
*** Starting SimpleHTTPServer on host server2 
*** Starting CLI:
```

#### Start POX
Start the Mininet controller (and wait for it to print some messages)
```no-highlight
> ./run_pox.sh
```

It will print messages that look like this:
```
POX 0.0.0 / Copyright 2011 James McCauley
server1 192.168.2.200
server2 172.24.3.30
client 10.0.1.100
vhost1-eth1 10.0.1.1
vhost1-eth2 10.0.2.1
vhost1-eth3 10.0.3.1
vhost2-eth1 10.0.2.2
vhost2-eth2 192.168.2.2
vhost2-eth3 192.168.3.1
vhost3-eth1 10.0.3.2
vhost3-eth2 172.24.3.2
vhost3-eth3 192.168.3.2
DEBUG:.home.ubuntu.pwospf.pox_module.pwospf.ofhandler:*** ofhandler: Successfully loaded ip settings for hosts
 {'vhost1-eth2': '10.0.2.1', 'vhost1-eth3': '10.0.3.1', 'server2': '172.24.3.30', 'server1': '192.168.2.200', 'vhost3-eth3': '192.168.3.2', 'vhost3-eth1': '10.0.3.2', 'vhost3-eth2': '172.24.3.2', 'client': '10.0.1.100', 'vhost2-eth3': '192.168.3.1', 'vhost2-eth2': '192.168.2.2', 'vhost2-eth1': '10.0.2.2', 'vhost1-eth1': '10.0.1.1'}

INFO:.home.ubuntu.pwospf.pox_module.pwospf.srhandler:created server
DEBUG:.home.ubuntu.pwospf.pox_module.pwospf.srhandler:SRServerListener listening on 8888
DEBUG:core:POX 0.0.0 going up...
DEBUG:core:Running on CPython (2.7.3/Aug 1 2012 05:14:39)
INFO:core:POX 0.0.0 is up.
This program comes with ABSOLUTELY NO WARRANTY.  This program is free software,
and you are welcome to redistribute it under certain conditions.
Type 'help(pox.license)' for details.
DEBUG:openflow.of_01:Listening for connections on 0.0.0.0:6633
Ready.
POX> INFO:openflow.of_01:[Con 1/1] Connected to 00-00-00-00-00-01
DEBUG:.home.ubuntu.pwospf.pox_module.pwospf.ofhandler:Connection [Con 1/1]
{'vhost1': {'eth3': ('10.0.3.1', 'fe:0c:ce:98:e6:c1', '10Gbps', 3)}}
{'vhost1': {'eth3': ('10.0.3.1', 'fe:0c:ce:98:e6:c1', '10Gbps', 3), 'eth2': ('10.0.2.1', 'ca:2a:c3:0a:c8:48', '10Gbps', 2)}}
{'vhost1': {'eth3': ('10.0.3.1', 'fe:0c:ce:98:e6:c1', '10Gbps', 3), 'eth2': ('10.0.2.1', 'ca:2a:c3:0a:c8:48', '10Gbps', 2), 'eth1': ('10.0.1.1', '36:fa:7f:22:db:65', '10Gbps', 1)}}
DEBUG:.home.ubuntu.pwospf.pox_module.pwospf.srhandler:SRServerListener catch RouterInfo even for vhost=vhost1, info={'eth3': ('10.0.3.1', 'fe:0c:ce:98:e6:c1', '10Gbps', 3), 'eth2': ('10.0.2.1', 'ca:2a:c3:0a:c8:48', '10Gbps', 2), 'eth1': ('10.0.1.1', '36:fa:7f:22:db:65', '10Gbps', 1)}, rtable=[('10.0.1.100', '10.0.1.100', '255.255.255.255', 'eth1'), ('10.0.2.0', '10.0.2.2', '255.255.255.0', 'eth2'), ('10.0.3.0', '10.0.3.2', '255.255.255.0', 'eth3')]
INFO:openflow.of_01:[Con 3/3] Connected to 00-00-00-00-00-03
DEBUG:.home.ubuntu.pwospf.pox_module.pwospf.ofhandler:Connection [Con 3/3]
{'vhost3': {'eth3': ('192.168.3.2', '02:1a:66:94:d6:94', '10Gbps', 3)}}
{'vhost3': {'eth3': ('192.168.3.2', '02:1a:66:94:d6:94', '10Gbps', 3), 'eth2': ('172.24.3.2', 'be:b8:be:fd:23:25', '10Gbps', 2)}}
{'vhost3': {'eth3': ('192.168.3.2', '02:1a:66:94:d6:94', '10Gbps', 3), 'eth2': ('172.24.3.2', 'be:b8:be:fd:23:25', '10Gbps', 2), 'eth1': ('10.0.3.2', '06:28:51:f8:1f:93', '10Gbps', 1)}}
DEBUG:.home.ubuntu.pwospf.pox_module.pwospf.srhandler:SRServerListener catch RouterInfo even for vhost=vhost3, info={'eth3': ('192.168.3.2', '02:1a:66:94:d6:94', '10Gbps', 3), 'eth2': ('172.24.3.2', 'be:b8:be:fd:23:25', '10Gbps', 2), 'eth1': ('10.0.3.2', '06:28:51:f8:1f:93', '10Gbps', 1)}, rtable=[('10.0.3.0', '10.0.3.1', '255.255.255.0', 'eth1'), ('172.24.3.30', '172.24.3.30', '255.255.255.255', 'eth2'), ('192.168.3.0', '192.168.3.1', '255.255.255.0', 'eth3')]
INFO:openflow.of_01:[Con 2/2] Connected to 00-00-00-00-00-02
DEBUG:.home.ubuntu.pwospf.pox_module.pwospf.ofhandler:Connection [Con 2/2]
{'vhost2': {'eth3': ('192.168.3.1', 'de:4a:68:6f:57:24', '10Gbps', 3)}}
{'vhost2': {'eth3': ('192.168.3.1', 'de:4a:68:6f:57:24', '10Gbps', 3), 'eth2': ('192.168.2.2', '7e:85:b3:94:93:91', '10Gbps', 2)}}
{'vhost2': {'eth3': ('192.168.3.1', 'de:4a:68:6f:57:24', '10Gbps', 3), 'eth2': ('192.168.2.2', '7e:85:b3:94:93:91', '10Gbps', 2), 'eth1': ('10.0.2.2', 'be:a6:3e:d3:3c:a2', '10Gbps', 1)}}
DEBUG:.home.ubuntu.pwospf.pox_module.pwospf.srhandler:SRServerListener catch RouterInfo even for vhost=vhost2, info={'eth3': ('192.168.3.1', 'de:4a:68:6f:57:24', '10Gbps', 3), 'eth2': ('192.168.2.2', '7e:85:b3:94:93:91', '10Gbps', 2), 'eth1': ('10.0.2.2', 'be:a6:3e:d3:3c:a2', '10Gbps', 1)}, rtable=[('10.0.2.0', '10.0.2.1', '255.255.255.0', 'eth1'), ('192.168.2.200', '192.168.2.200', '255.255.255.255', 'eth2'), ('192.168.3.0', '192.168.3.2', '255.255.255.0', 'eth3')]
```

#### Start Reference Solution

##### Running Multiple Routers
Because this assignment requires multiple instances of your router to run simultaneously you will want to use the -r and the -v command line options. -r allows you to specify the routing table file you want to use (e.g. -r rtable.vhost1) and -v allows you to specify the host you want to connect to on the topology (e.g. -v vhost3). We also provide a script (run_sr.sh) to help you to run multiple instances of sr. Note that, **You have to run each sr on a different machine, since they all bind to the same port.**

First, you will need to figure out the IP of your machine runs Mininet/POX:
```no-highlight
> curl ifconfig.me
A.B.C.D
```
A.B.C.D will be your public IP. 

Now, on the same machine, connect your reference solution, sr_solution, to vhost1. Replace A.B.C.D with the actual IP of your Mininet/POX machine.
```no-highlight
> ./run_sr.sh A.B.C.D vhost1
```






