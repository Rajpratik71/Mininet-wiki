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

The default _IP_CONFIG_ and _rtable_ should look like the following:

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
#### Configure the environment by running the config.sh file
```no-highlight
> cd ~/cs144_lab3/
> ./config.sh
```
#### Start Mininet emulation by using the following command
```no-highlight
> cd ~/cs144_lab3/
> ./run_mininet.sh
```
You should be able to see some output like the following: 

```
*** Shutting down stale SimpleHTTPServers  
*** Shutting down stale webservers  
server1 192.168.2.2
server2 172.64.3.10
client 10.0.1.100
sw0-eth1 192.168.2.1
sw0-eth2 172.64.3.1
sw0-eth3 10.0.1.1
*** Successfully loaded ip settings for hosts
 {'server1': '192.168.2.2', 'sw0-eth3': '10.0.1.1', 'sw0-eth1': '192.168.2.1', 'sw0-eth2': '172.64.3.1', 'client': '10.0.1.100', 'server2': '172.64.3.10'}
*** Creating network
*** Creating network
*** Adding controller
*** Adding hosts:
client server1 server2 
*** Adding switches:
sw0 
*** Adding links:
(client, sw0) (server1, sw0) (server2, sw0) 
*** Configuring hosts
client server1 server2 
*** Starting controller
*** Starting 1 switches
sw0 
*** setting default gateway of host server1
server1 192.168.2.1
*** setting default gateway of host server2
server2 172.64.3.1
*** setting default gateway of host client
client 10.0.1.1
*** Starting SimpleHTTPServer on host server1 
*** Starting SimpleHTTPServer on host server2 
*** Starting CLI:
mininet> 
```
Keep this terminal open, as you will need the mininet command line for debugging. Now, use another terminal to continue the next step. (Do not do ctrl-z) 

#### Mininet requires a controller, which we implemented in POX. To run the controller, use the following command to run the controller:
```no-highlight
> cd ~/cs144_lab3/
> ln -s ../pox
> ./run_pox.sh
```
You should be able to see some output like the following:

```
POX 0.0.0 / Copyright 2011 James McCauley
DEBUG:.home.ubuntu.cs144_lab3.pox_module.cs144.ofhandler:*** ofhandler: Successfully loaded ip settings for hosts
 {'server1': '192.168.2.2', 'sw0-eth3': '10.0.1.1', 'sw0-eth1': '192.168.2.1', 'sw0-eth2': '172.64.3.1', 'client': '10.0.1.100', 'server2': '172.64.3.10'}

INFO:.home.ubuntu.cs144_lab3.pox_module.cs144.srhandler:created server
DEBUG:.home.ubuntu.cs144_lab3.pox_module.cs144.srhandler:SRServerListener listening on 8888
DEBUG:core:POX 0.0.0 going up...
DEBUG:core:Running on CPython (2.7.3/Aug 1 2012 05:14:39)
INFO:core:POX 0.0.0 is up.
This program comes with ABSOLUTELY NO WARRANTY.  This program is free software,
and you are welcome to redistribute it under certain conditions.
Type 'help(pox.license)' for details.
DEBUG:openflow.of_01:Listening for connections on 0.0.0.0:6633
Ready.
POX> 
```
**Please note that you have to wait the Mininet to connect to the POX controller before you continue to the next step**. Once the Mininet connected, you will see the following output:

```
INFO:openflow.of_01:[Con 1/249473472573510] Connected to e2-e5-11-b6-b0-46
DEBUG:.home.ubuntu.cs144_lab3.pox_module.cs144.ofhandler:Connection [Con 1/249473472573510]
DEBUG:.home.ubuntu.cs144_lab3.pox_module.cs144.srhandler:SRServerListener catch RouterInfo even, info={'eth3': ('10.0.1.1', '86:05:70:7e:eb:56', '10Gbps', 3), 'eth2': ('172.64.3.1', 'b2:9e:54:d8:9d:cd', '10Gbps', 2), 'eth1': ('192.168.2.1', '36:61:7c:4f:b6:7b', '10Gbps', 1)}, rtable=[]
```
Keep the POX running. Now, open yet another terminal to continue the next step.  (Dont’ do ctrl-z)

#### Now you are ready to test out the connectivity of the environment setup. To do so, run the binary file of the solution “sr_solution”
```no-highlight
> cd ~/cs144_lab3/
> ./sr_solution
```
You should be able to see some output like the following:

```
Loading routing table from server, clear local routing table.
Loading routing table
---------------------------------------------
Destination     Gateway         Mask    Iface
10.0.1.100              10.0.1.100      255.255.255.255 eth3
192.168.2.2             192.168.2.2     255.255.255.255 eth1
172.64.3.10             172.64.3.10     255.255.255.255 eth2
---------------------------------------------
Client ubuntu connecting to Server localhost:8888
Requesting topology 0
successfully authenticated as ubuntu
Loading routing table from server, clear local routing table.
Loading routing table
---------------------------------------------
Destination     Gateway         Mask    Iface
10.0.1.100              10.0.1.100      255.255.255.255 eth3
192.168.2.2             192.168.2.2     255.255.255.255 eth1
172.64.3.10             172.64.3.10     255.255.255.255 eth2
---------------------------------------------
Router interfaces:
eth3    HWaddr86:05:70:7e:eb:56
        inet addr 10.0.1.1
eth2    HWaddrb2:9e:54:d8:9d:cd
        inet addr 172.64.3.1
eth1    HWaddr36:61:7c:4f:b6:7b
        inet addr 192.168.2.1
 <-- Ready to process packets --> 
```

In this particular setup, 192.168.2.2 is the IP for server1, and 172.64.3.10 is the IP for server2.  You can find the IP addresses in your IP_CONFIG file.

Now, back to the terminal where Mininet is running. To issue an command on the emulated host, type the host name followed by the command in the Mininet console. For example, the following command issues 3 pings from the client to the server1.

```no-highlight
mininet> client ping -c 3 192.168.2.2
```
You should be able to see the following output. 
```
PING 192.168.2.2 (192.168.2.2) 56(84) bytes of data.
64 bytes from 192.168.2.2: icmp_req=1 ttl=63 time=66.9 ms
64 bytes from 192.168.2.2: icmp_req=2 ttl=63 time=49.9 ms
64 bytes from 192.168.2.2: icmp_req=3 ttl=63 time=68.8 ms
```

You can also use traceroute to see the route between client to server1.
```no-highlight
mininet> client traceroute -n 192.168.2.2
```
You should be able to see the following output. 
```
traceroute to 192.168.2.2 (192.168.2.2), 30 hops max, 60 byte packets
 1  10.0.1.1  146.069 ms  143.739 ms  143.523 ms
 2  192.168.2.2  226.260 ms  226.070 ms  225.868 ms
```

Finally, to test the web server is properly working at the server1 and server2, issue an HTTP request by using _wget_ or _curl_.
```no-highlight
mininet> client wget http://192.168.2.2
```
You should be able to see the following output. 
```
--2012-12-17 06:52:23--  http://192.168.2.2/
Connecting to 192.168.2.2:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 161 [text/html]
Saving to: `index.html'

     0K                                                       100% 17.2M=0s

2012-12-17 06:52:24 (17.2 MB/s) - `index.html' saved [161/161]
```
