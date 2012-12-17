## Introduction

In this exercise we will study the dynamics of TCP in home networks. Take a look at the figure below which shows a “typical” home network with a Home Router connected to an end host. The Home Router is connected via Cable or DSL to a Headend router at the Internet access provider’s office. We are going to study what happens when we download data from a remote server to the End Host in this home network.

![alt text](http://yuba.stanford.edu/~huangty/mininet/Bufferbloat.png "Topology for NAT")

In a real network it’s hard to measure cwnd (because it’s private to the Server) and the buffer occupancy (because it’s private to the router). To make our measurement job easier, we are going to emulate the network in Mininet (See [[Environment Setup]] for setting up the environment).

The goals of the exercise are to:
 + Learn first-hand the dynamics of cwnd and buffer occupancy in a “real” network.
 + Learn why large router buffers can lead to poor performance in home networks. This problem is often called “Buffer Bloat.”
 + Learn how to use Mininet so you can repeat or extend the experiments in your own time.

