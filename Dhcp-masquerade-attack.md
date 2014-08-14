### Introduction

In this demonstration, we construct a simple network to demonstrate a DHCP attack.

The test network consists of several hosts connected to a single switch:

* `h1` is the "victim" host - a computer which has just connected to the network and is going to make DHCP requests.
* `dhcp` is the "good" DHCP server which provides correct information, but which is connected to the switch by a slower link
* `evil` is a malicious host which is connected directly to the switch by a fast link; it provides malicious DHCP responses and also hosts a malicious DNS server and malicious DHCP server.

### Code
The code is available via `git`:

    git clone https://bitbucket.org/lantz/cs144-dhcp

### Video

A video of the DHCP attack demo is available at:

https://bitbucket.org/lantz/cs144-dhcp/downloads/firefox-dhcp-4-720p.mov