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
This assignment runs on top of Mininet which was built at Stanford. Mininet allows you to emulate a topology on a single machine. It provides the needed isolation between the emulated nodes so that your router node can process and forward real Ethernet frames between the hosts like a real router. You don't have to know how Mininet works to complete this assignment, but more information about Mininet (if you're curious) is available here. 



##### checkout code
* cd ~
* git clone https://huangty@bitbucket.org/huangty/cs144_lab3.git
* cd cs144_lab3/
* git checkout --track remotes/origin/standalone

##### install pox module
* cd ~/cs144_lab3
* ./config