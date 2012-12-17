###Introduction
In this lab assignment you will be writing a simple router with a static routing table. Your router will receive raw Ethernet frames. It will process the packets just like a real router, then forward them to the correct outgoing interface. We’ll make sure you receive the Ethernet frames; your job is to create the forwarding logic so packets go to the correct interface.

Your router will route real packets from your machines to application servers sitting behind your router. The application servers are running an HTTP server. When you have finished the forwarding path of your router, you should be able to access these servers using regular client software. In addition, you should be able to ping and traceroute to and through a functioning Internet router. A sample routing topology is shown below:

![alt text](http://yuba.stanford.edu/~huangty/mininet/sr_topo.png "Topology for Simple Router")


##### checkout code
* cd ~
* git clone https://huangty@bitbucket.org/huangty/cs144_lab3.git
* cd cs144_lab3/
* git checkout --track remotes/origin/standalone

##### install pox module
* cd ~/cs144_lab3
* ./config
