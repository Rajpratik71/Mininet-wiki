<!-- %META:TOPICINFO{author="BobLantz" date="1340917780" format="1.1" reprev="1.2" version="1.2"}% -->
<!-- %META:TOPICPARENT{name="MininetDocumentation"}% -->
<!-- Use
 our custom page layout:
* Set VIEW_TEMPLATE = [MininetView](MininetView)
-->


### Viewing Documentation for Mininet's Python API

Mininet's modules and Python API are documented using Python documentation strings which may be accessed from Python's regular <code>help()</code> mechanism. For example,
<verbatim>
python
>>> from mininet.node import Host
>>> help(Host.IP)
Help on method IP in module mininet.node:
IP(self, intf=None) unbound mininet.node.Host method
        Return IP address of a node or specific interface.</verbatim>

You may also wish to generate HTML documentation using <code>doxypy</code>:

<verbatim>
sudo apt-get install doxypy
cd ~/mininet
make doc
python -m [SimpleHTTPServer](SimpleHTTPServer) &</verbatim>

At this point, you can point a web browser to port 8000 of the host that Mininet is running on and browse the documentation for each of Mininet's classes.

Additionally, Mininet includes several useful pieces of sample code in <code>[mininet/examples](https://github.com/mininet/mininet/tree/master/examples)</code>.
