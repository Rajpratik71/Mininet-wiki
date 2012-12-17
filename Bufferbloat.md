## Introduction

In this exercise we will study the dynamics of TCP in home networks. Take a look at the figure below which shows a “typical” home network with a Home Router connected to an end host. The Home Router is connected via Cable or DSL to a Headend router at the Internet access provider’s office. We are going to study what happens when we download data from a remote server to the End Host in this home network.

![alt text](http://yuba.stanford.edu/~huangty/mininet/Bufferbloat.png "Bufferbloat")

In a real network it’s hard to measure cwnd (because it’s private to the Server) and the buffer occupancy (because it’s private to the router). To make our measurement job easier, we are going to emulate the network in Mininet (See [[Environment Setup]] for setting up the environment).

The goals of the exercise are to:
 + Learn first-hand the dynamics of cwnd and buffer occupancy in a “real” network.
 + Learn why large router buffers can lead to poor performance in home networks. This problem is often called “Buffer Bloat.”
 + Learn how to use Mininet so you can repeat or extend the experiments in your own time.

## Part 1: Get Mininet up and running 
![alt text](http://yuba.stanford.edu/~huangty/mininet/Bufferbloat_topo.png "Topology for Bufferbloat")

### Get the Bufferbloat Topology 
Bring up a terminal on your machine 
```no-highlight
> git clone https://bitbucket.org/huangty/cs144_bufferbloat.git
```
### Run the Mininet Emulator
```no-highlight
> cd cs144_bufferbloat/
> sudo ./run.sh 
```

### Measure the Delay Between the Two Hosts
After Mininet is running, you can measure the delay from H1 to H2 with the command: 
mininet> h1 ping -c 10 h2

## Part 2: Web page download - Sketch the TCP CWND

### Measure how long it takes to download a web page from H1
```no-highlight
mininet> h2 wget http://10.0.0.1
```

### Sketch how you think cwnd evolves over time at H1.  Mark multiples of RTT on the x-axis.
![alt text](http://yuba.stanford.edu/~huangty/mininet/Bufferbloat_axes.png "TCP AXES")

## Part 3: “Streaming video” - Sketch the TCP CWND and Buffer Occupancy.

### Create the Video Flow
To see how the dynamics of a long flow (which enters the AIMD phase) differs from a short flow (which never leaves slow-start), we are going to repeat Part 2 for a “streaming video flow”. Instead of actually watching videos on your machine, we are going to set up a long-lived high speed TCP connection instead, to emulate a long-lived video flow.  You can generate long flows using the _iperf_ command, and we have wrapped it in a script which you can run as follows: 
```no-highlight
mininet> h1 ./iperf.sh  
```

You can see the throughput of TCP flow from H1 to H2 by running:
```no-highlight
mininet> h2 tail -f ./iperf-recv.txt 
```
You can quit viewing throughput by pressing CTRL-C.

### The TCP CWND of the Video Flow
2.	Sketch how you think cwnd evolves over time at H1. You might find it useful to use ping to measure how the delay evolves over time, after the iperf has started:
```no-highlight
mininet> h1 ping -c 100 h2
```
