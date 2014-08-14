### Introduction

In this demonstration, we construct a simple network to demonstrate a DHCP attack.

The test network consists of several hosts connected to a single switch:

* `h1` is the "victim" host - a computer which has just connected to the network and is going to make DHCP requests.
* `dhcp` is the "good" DHCP server which provides correct information, but which is connected to the switch by a slower link (500 ms of delay in this example.)
* `evil` is a malicious host which is connected directly to the switch by a fast link; it provides malicious DHCP responses and also hosts a malicious DNS server and malicious DHCP server.

When the victim host `h1` makes the DHCP request, it is forwarded to both `dhcp` and `evil`, but `evil` responds first and its DHCP offer is accepted by the victim. The `evil` DHCP server provides its address as the DNS server address, so the victim sends its DNS requests to the `evil` DNS server, which provides its own IP address rather than the correct IP address for the DNS lookup. The victim then connects to the address of the `evil` web server, which serves its own malicious content, asking the victim for an e-mail address and password.

### Code
The code is available via `git`:

    git clone https://bitbucket.org/lantz/cs144-dhcp

### Video

A video of the DHCP attack demo is available at:

https://bitbucket.org/lantz/cs144-dhcp/downloads/firefox-dhcp-4-720p.mov