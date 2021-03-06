This page is intended to be a simple reference to community-developed extensions to Mininet and other packages and tools which may be of interest to Mininet users.

(Note that one of the GSoC participants is prototyping a PyPI-like repository for Mininet-compatible Python packages, so that may be available in the future!)

If links are broken, please fix them - thanks! Please also feel free to improve the text descriptions.

**Also see [[Mininet Apps and Tools]]**

#### Mininet Installation Packages for Other OSes

* RHEL/Scientific 6: http://puias.princeton.edu/data/puias/computational/6/SRPMS/mininet-2.0.0-2.puias6.src.rpm

* CentOS/Scientific Linux: http://downloads.naulinux.ru/pub/NauLinux/6.3/x86_64/Extras/RPMS/Projects/OpenFlow/

#### Mininet Porting for TinyCore Linux

* TinyCore Linux: https://github.com/jediMunees/mininetTinycore/wiki

#### Useful Tools for Mininet Users

* Visual Network Description - VND (http://www.ramonfontes.com/vnd) - A GUI tool that allows automatic creation of Mininet and Openflow Controllers Scripts.

* Chef cookbook for installing Mininet: https://github.com/karlll/mininet-cookbook.git

* Mininet Editor (https://thomaash.github.io/me/) - Web based topology editor for Mininet. Similar to MiniEdit, but with nicer GUI, real-time input validation, addressing plan export, script import and many more features.

#### Customized Mininet Versions and VMs

* Mininet for CCNx (content-centric networking): https://github.com/carlosmscabral/mn-ccnx/wiki


#### Mininet-based Systems

* [IPMininet](https://github.com/cnp3/ipmininet) is a python library, extending Mininet, in order to support emulation of (complex) IPv4 and IPv6 networks. As such, it provides new classes, such as Routers, auto-configures all properties not set by the user, such as IP addresses or router configuration files,... The latest user documentation is available on https://ipmininet.readthedocs.io/.

#### Reproducible Research Experiments

* http://reproducingnetworkresearch.wordpress.com includes a variety of reproducible networking experiments implemented using Mininet.

#### New Topologies (and Custom Controllers)

* [RipL-POX](https://github.com/brandonheller/riplpox) is a multipath-capable controller written in POX. The README explains how to create a Fat Tree topology using Mininet and use it with RipL-POX as the controller.

#### New Switch and Host Classes