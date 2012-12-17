##Introduction
In this lab assignment you will be writing a simple router with a static routing table. Your router will receive raw Ethernet frames. It will process the packets just like a real router, then forward them to the correct outgoing interface. We’ll make sure you receive the Ethernet frames; your job is to create the forwarding logic so packets go to the correct interface.

Your router will route real packets from a emulated host (client) to two emulated application servers (http server 1/2) sitting behind your router. The application servers are each running an HTTP server. When you have finished the forwarding path of your router, you should be able to access these servers using regular client software. In addition, you should be able to ping and traceroute to and through a functioning Internet router. A sample routing topology is shown below:

![alt text](http://yuba.stanford.edu/~huangty/mininet/sr_topo.png "Topology for Simple Router")

If the router is functioning correctly, all of the following operations should work:

* Pinging from the client to any of the router's interfaces (192.168.2.1, 172.64.3.1, 10.0.1.1). 
* Tracerouting from the client to any of the router's interfaces
* Pinging from the client to any of the app servers (192.168.2.2, 172.64.3.10)
* Tracerouting from the client to any of the app servers
* Downloading a file using HTTP from one of the app servers

Additional requirements are laid out in the 'Requirements' section.

## Mininet
This assignment runs on top of Mininet which was built at Stanford. Mininet allows you to emulate a topology on a single machine. It provides the needed isolation between the emulated nodes so that your router node can process and forward real Ethernet frames between the hosts like a real router. You don't have to know how Mininet works to complete this assignment, but more information about Mininet (if you're curious) is available [here](http://yuba.stanford.edu/foswiki/bin/view/OpenFlow/Mininet). 

## Get Started
### Environment Setup
Please refer to [[Environment Setup]] for setting up the environment.

### Checkout Starter Code
* cd ~
* git clone https://huangty@bitbucket.org/huangty/cs144_lab3.git
* cd cs144_lab3/
* git checkout --track remotes/origin/standalone

### Install Simple Router POX module
* cd ~/cs144_lab3
* ./config

### Configuration Files
There are two configuration files. 
* ~/cs144_lab3/IP_CONFIG: Listed out the IP addresses assigned to the emulated hosts. 
* ~/cs144_lab3/router/rtable (also linked to ~/cs144_lab3/rtable): The static routing table used for the simple router. 

Feel free to manipulate these two files to change the IP addresses of the hosts or the routing table in the routing table. The default _IP_CONFIG_ and _rtable_ should look like the following:

```no-highlight
> cat ~/cs144_lab3/IP_CONFIG
server1 192.168.2.2
server2 172.64.3.10
client    10.0.1.100
sw0-eth1 192.168.2.1
sw0-eth2 172.64.3.1
sw0-eth3 10.0.1.1
```
```no-highlight
> cat ~/cs144_lab3/rtable
10.0.1.100  10.0.1.100  255.255.255.255 eth3
192.168.2.2 192.168.2.2 255.255.255.255 eth1
172.64.3.10  172.64.3.10  255.255.255.255 eth2
```

### Test Connectivity of Your Emulated Topology
* Configure the environment by running the config.sh file
```no-highlight
> cd ~/cs144_lab3/
> ./config.sh
```
* Start Mininet emulation by using the following command
```no-highlight
> cd ~/cs144_lab3/
> ./run_mininet.sh
```
You should be able to see some output like the following: 

> *** Shutting down stale SimpleHTTPServers
> *** Shutting down stale webservers 
server1 107.21.41.195
server2 107.21.17.129
sw0-eth1 107.23.34.64
sw0-eth2 107.21.14.129 
sw0-eth3 10.0.1.11
*** Successfully loaded ip settings for hosts {'sw0-eth1': '107.23.34.64', 'sw0-eth2': '107.21.14.129', 'sw0-eth3': '10.0.1.11', 'server1': '107.21.41.195', 'server2': '107.21.17.129'}
*** Creating network
*** Creating network
*** Adding controller
*** Adding hosts: root server1 server2
*** Adding switches: sw0
*** Adding links: (root, sw0) (server1, sw0) (server2, sw0) 
*** Configuring hosts root server1 server2
*** Starting controller
*** Starting 1 switches sw0 
*** setting mac address of sw0-eth3 the same as eth1 (0a:45:07:00:1a:82)
*** setting default gateway of host server1 server1 107.21.41.195
*** setting default gateway of host server2 server2 107.21.17.129
*** Starting SimpleHTTPServer on host server1
*** Starting SimpleHTTPServer on host server2
*** Starting CLI:
mininet>

* 