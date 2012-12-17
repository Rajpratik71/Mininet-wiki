## Introduction 
In this lab assignment you will be writing a simple NAT that can handle ICMP and TCP. It will implement a subset of the functionality specified by RFC5382 and RFC5508. Expect to refer often to these RFCs.

Before beginning this lab, it is crucial that you:
* Understand how NATs work. Consider re-watching the NAT lectures.
* Understand TCP handshake and teardown packet sequences. Consider working through the TCP state diagram.
* Understand NAT Endpoint Independence.
NAT builds on "Simple Router". You should start with your static router code and extend it to include NAT functionality.

As with "Simple Router", we will create a NAT that sits in Mininet between the app servers and the client.  The internal interface of the NAT faces the client, while the external interfaces are connected to app servers. The app servers are "outside" the NAT, while the client is "inside."  

The topology of NAT is as follows, where the NAT's internal interface (eth1) faces the client and its external interface (eth2) has two application servers connected with a switch:

A correct implementation should support the following operations from the myth machines:

* Pinging the NAT's internal interface from myth machines
* Pinging any of the app servers (e.g. 184.72.104.217, 107.23.87.29 above)
* Downloading files using HTTP from the app servers
All packets to external hosts (app servers) should appear to come from eth2's address (e.g. 184.72.104.221 above).



