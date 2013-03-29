## Mininet Project Ideas

This page summarizes various ideas for **features and future functionality for Mininet**. It may be used as source material for potential features for any Mininet developer to implement, as well as potential projects for Google Summer of Code or other mentoring programs or internships.

## Background

[Mininet](http://mininet.github.com) is a **lightweight emulator for computer networks and distributed systems**. It starts up a virtual network/distributed system on your laptop in a few seconds, with a singe command, and provides an extensive Python API for emulating pretty much any Linux-based network system you can imagine (though perhaps at lower speed or smaller scale.) It is widely used for network research, teaching, and development, particularly in the area of OpenFlow and Software-Defined Networking. It is included in Ubuntu Linux as the "mininet" package.

## Google Summer of Code Information

Mininet is an exciting and high-impact project which is also fun to work on, so we're considering applying to the Google Summer Of Code (GSOC).

**Mentoring**: A participant in GSOC would choose one or more projects and be advised by a project mentor, either Mininet project founder **[Bob Lantz](https://github.com/lantz)** or another member of the Mininet development team or community.

**Projects**: The exact projects that participants would work on would depend on their interest and expertise. The projects are grouped in approximate order of difficulty as well as whether we have starter code. Projects should be approved as agreements between the potential mentor(s) and participant(s), based on mentor availability and interest, project impact, project difficulty, and participant interest, experience and knowledge. 

**Prerequisites**: In general, working on Mininet requires knowledge of Python and a sound understanding of computer networks, operating systems, and distributed systems (preferably at the undergraduate, masters or professional level.) Other specific projects may require knowledge of C and Unix/Linux systems programming and possibly kernel programming as well.

**Process**: GSOC projects will follow the standard Mininet development process!

## Mininet Development Process

Mininet follows a fairly standard development process on Github:

1. **Pick a project** to work on or a feature to implement

2. **Check the Mininet [issues page](https://github.com/mininet/mininet/issues)** to see if someone is already working on it.

3. If so, **contact the person who is working on it**, and also **add a note to the issue** when you start working on it.

4. **Fork/clone** the Mininet repo (master branch) on github, and **work on your new feature**

5. When you have something that is working or are ready for feedback on your feature, **submit a pull request**.

6. **Answer any questions** you may get on the pull request, and **revise your code** as necessary.

7. If you have specific (and intelligent/well-resarched) development questions that you cannot answer on your own, send them to the **[mininet-dev](https://mailman.stanford.edu/mailman/listinfo/mininet-dev)** mailing list. You can also ask for additional feedback on your code/pull request.

8. When/if the feature is complete and approved by the Mininet developers, they may choose to **merge it** into the main Mininet source tree (either master or a development branch)!! Congratulations, you are now an official Mininet contributor!!

## Making it Real: extending existing prototype code into an official feature

### Hardware interface support

**Brief explanation**: It's useful to be able to add hardware interfaces to a Mininet network, and also to integrate a Mininet network into a "real" network. Currently examples/hwintf.py contains code demonstrating how to add a "real" hardware interface (or any interface not created/controlled by Mininet) to a Mininet switch. Unfortunately, hardly anyone on the  mailing list seems to be able to understand it or use it, usually because they don't have a good understanding of the principles or practical aspects of IP networking or Linux.

Questions: do we need a better API for this? Do we need better example code? Do we need resources to help users improve their (often meager) skills?

It would certainly be nice to have an easy mechanism for plugging a Mininet virtual network into a physical network. Is there an easy way to provide some mechanism? A problem is that it usuallly requires configuration of network switches/routers, something which is beyond the scope of Mininet (or is it?) 
Access to “real” Internet from mininet network (e.g. extra interfaces)

A common complaint from Mininet users is that "Google doesn't work" or "apt-get doesn't work." This complaint usually indicates a fundamental misunderstanding of Mininet's default configuration:  by default, Mininet gives you a 10.x data network which is not connected to the outside world. This is often a good thing! However, this is not what many users expect - most real networks connect to the internet, after all. It would be easy to follow Emulab's approach of giving each host two interfaces, one for the (internal, OpenFlow-controlled) data network, and one on the LAN. However, there are issues: how are LAN addresses assigned? Can we assume that there is a DHCP server and that there are enough IP addresses on the LAN segment for Mininet hosts? Or should we implement a local network with NAT to allow Mininet hosts to share the Linux host's LAN IP address? Probably we want both, but I suspect that the NAT approach is more likely to be what most people would want. See "Automatic NAT Iimplementation", below.

**Expected results**: A simple and robust API for integrating real hardware interfaces into a Mininet network.

**Knowledge prerequisite**: Understanding of Linux/IP networking, VM network bridging, Mininet and Python.

### Access into Mininet from host and/or local network

On the other hand, it's also useful to be able to connect into the Mininet network either from the host or the  LAN. sshd.py shows how to create an interface on the local host (in the root namespace) which is connected to a Mininet switch, and also how to start up sshd on each Mininet host so that you can ssh into the Mininet network.

**Expected results**: A command-line option for `mn` which automatically provides access to the Mininet network from the host or LAN.

**Knowledge prerequisite**: Understanding of Linux/IP networking, VM network bridging, Mininet and Python.

### sshd support

**Brief explanation**:It might be nice to have a means of automatically starting up sshd (and shutting it down!) on each Mininet host. As noted above, sshd.py has a prototype implementation for this feature, but getting it right, reliable, etc. is actually tricky. There's another more subtle issue and that is: what is the best way to enable seamless transitions between Mininet and hardware? Hardware systems are often bound together using ssh/sshd, so does it make sense to have a Mininet network work that way (ssh and paramiko in Python?) Unfortunately this would increase overhead and startup time, and one of the huge benefits of Mininet is that it starts up very quickly for a highly efficient edit-run-debug loop.

**Expected results**: A command-line option for `mn`, and either an API or extension to the `Mininet` class which automatically starts up (and shuts down) `sshd` on each Mininet host; possibly this would be integrated with the above function of providing access to Mininet hosts from the local host or LAN.

**Knowledge prerequisite**: Understanding of Linux/IP networking, VM network bridging, Mininet and Python.

### Mininet GUIs: consoles.py and miniedit.py

**Brief explanation**: I (BL) wrote the consoles.py and miniedit.py examples to show what I thought was a really exciting and cool thing you could do in Mininet: make GUI programs that create and use a live virtual network interactively! It seemed so obvious that I had done much of the hard work and that any smart Python/GUI programmer could take what I had done either as code or inspiration and run with it. Unfortunately, that never happened. We could decide to expand these programs into (more) full-blown applications, or we could also try to make the code reusable as a framework. Or we could write documentation about the design of the apps and how to expand them. Or perhaps we could rewrite them in Qt or a more "modern" GUI framework. Regardless, I still feel that GUI apps using Mininet have huge potential, as do graphical interfaces for core Mininet functionality.

**Expected results**: significantly more beautiful, functional, robust and useful consoles.py and miniedit.py applications. In the case of consoles.py, it should provide a useful API for building monitoring applications. In the case for miniedit.py, it should be able to save and load networks as mininet-compatible topologies (expressed in Python.)

**Knowledge prerequisite**: Understanding of Linux/IP networking, Mininet, Python, OpenFlow and TkInt and/or another GUI framework. HCI/user-interface design abilities.

### Automatic NAT implementation

**Brief explanation**: As noted above, an automatic NAT implementation (e.g. nat=True or --nat) would be nice to have to enable Mininet hosts to talk to the outside world by default.

**Expected results**: A command-line option for `mn`, and probably an option to `Mininet()`, which provides automatic creation of a NAT which allows transparent, seamless access to the LAN and the internet from Mininet hosts.

**Knowledge prerequisite**: Understanding of Linux/IP networking, VM network bridging, IPTables, NAT, Mininet and Python.

## "Easier" Projects

### Mininet error checking and diagnostics/status monitoring

**Brief explanation**: Currently when Mininet isn't working (e.g. controller can't connect to switches, or packets aren't being forwarded in your network), the only indication that something is wrong is that ping doesn't work. It would be nice to have some means of verifying network health, for example, by monitoring whether all switches are connected to controllers, or by checking that all links in the system work somehow. The former would be easy(ish?), the latter hard.

**Expected results**: A command-line option for `mn` which automatically provides access to the Mininet network from the host or LAN.

**Knowledge prerequisite**: Understanding of Linux/IP networking, VM network bridging, Mininet code base and Python. Usage-level understanding of Open vSwitch and the OpenFlow reference switch.

### Better X11 support

**Brief explanation**: It should be possible to start up arbitrary X11 apps from the mininet> prompt.

**Difficulty**: Trivial. ;-)

**Expected results**: A CLI command for starting X11 apps

**Knowledge prerequisite**: Understanding of Mininet API and CLI code base and X11.

### Easier VM image creation (e.g. using something like ubuntu-vm-builder)

**Brief explanation**: Currently we make the VM images by hand - it's a pain, and it's why the Mininet VMs are rarely updated. We could automate this by using a script like the now-deprecated ubuntu-vm-builder, which was part of Ubuntu and used debuild. Or we could script Xen/KVM/VMware/Vbox to take an off-the-shelf Ubuntu image and install Mininet in it. There might be other better ways.

**Expected results**: Modified Mininet makefiles which include a target to build a VM image.

**Knowledge prerequisite**: Understanding of VM creation and things like debuild, chroot; understanding of Linux/Unix tools like Make, shell scripting, possible Python scripting.

### Installing NOX 2 and/or other controllers from `install.sh`

**Brief explanation**: We currently install POX rather than NOX classic by default with install.sh -a (which is intended to create a VM image for the OpenFlow tutorial.) It might be nice to add NOX2 or other useful software.

**Expected results**: Additional options in `install.sh` to install additional controllers or other useful software.

**Knowledge prerequisite**: Understanding of Mininet, OpenFlow controllers, Linux software installation, and shell scripting.

### Real API documentation explaining how to use the API (will be helped somewhat by integrating CS244 intro document)

**Brief explanation**: CS244's Introduction to Mininet has been revised and moved into the Mininet wiki on github. However, we're still lacking in high-quality documentation describing the overall API (and system) architecture as well as how to use the low-, mid- and high-level APIs. Ultimately I'd like to see high quality documentation (think Python, Smalltalk-80: The Language and its Implementation, or Computer Architecture: A Quantitative Approach) but something of O'Reilly quality might be a nice stopgap.

**Expected results**: A document describing the API and system architecture.

**Knowledge prerequisite**: Familiarity with Python and the Mininet API. Ability to write Mininet scripts. Ability to read and understand Python APIs and to write clear and thorough documentation.

### Automatic/easy update for Mininet (esp. in VM image)

**Brief explanation**: If there were an easy way to update Mininet in the VM image, then we might not have to update the whole VM image simply for a Mininet change. Perhaps the tutorial image should update Mininet the first time it boots up? Or maybe we should add update instructions to the OpenFlow tutorial?

### Additional easy (but non-critical)  bug fixes

**Brief explanation**: There are a number of easy(ish) bug fixes which were deferred from 2.0.0. We can probably identify them and fix them.

### Patch bay object

**Brief explanation**: Mininet 2.0 switches support attachment and reattachment. This allows you to simulate mobility or VM migration. A more generalized mechanism might allow for everything to be connected to a patch bay/patch panel object (probably just a large OpenFlow switch) which could be used to allow arbitrary reconnection of any interfaces. Effectively this means that each veth pair is split into two veth pairs which are then plugged into the patch bay. This would require creation of a new object (PatchBay?) and support for using it if desired. Note that the overall system load would be increased - this is a trade-off between flexibility and performance, so we would not wish to make it mandatory. This could also be thought of as an L1 switch, although really it's L1.5 since everything is still Ethernet - the L2/MAC protocol cannot be changed beyond what OpenFlow already allows you to do.

### “testbed mode” - Hosts optionally on both control and data networks

**Brief explanation**: As noted, Emulab and other testbeds provide (at least) two interfaces on each host: one on the private data network, and one on either a control network or the LAN. This would, to some extent, solve the problem of people who want to access the internet from Mininet hosts, and it would allow network-based (as upposed to pipe or API-based) access to hosts even when the data network was not working.

### Additional parametrized topologies (fat tree, jellyfish, mesh, random, etc.) bundled with easy controller support for multipath

**Brief explanation**: Parametrized topologies are one of Mininet's most useful features. Unfortunately only a few topologies (tree, linear, single switch) are included in the box. It would be great if Mininet came with more topologies and if there were a readily available controller that supported multipath. We can probably leverage the CS244 work to some extent, since Brandon provided a fat tree topology as well as a simple multipath controller (riplpox) - both of which were extremely handy in CS244. Additionally, two of the CS244 projects included Jellyfish and DCell, so perhaps they have topology code we could use or use as inspiration.

### Raspberry Pi Mininet image/"network in a box" ;-)

**Brief explanation**: This would be a fun and cool project and would also allow for a very inexpensive "network in a box" which could be used not only as a dirt-cheap Mininet platform (for courses, workshops, tutorials, anyone who wants one) but also as virtual "hardware" which you can plug your PC, server, or switches into to get a larger virtual topology. The basics are extremely easy, but there are some interesting and fun possibilities here.

## "Advanced"/more challenging projects

### More sample, downloadable SDN systems (including controller and applications)

**Brief explanation**: One of the main goals of Mininet was to enable people to easily share networks and build upon the work of others. To some extent this was realized in CS244 and our blog, reproducingnetworkresearch.wordpress.com. However, we don't currently have a repository (besides EC2) for people to contribute downloadable images and systems that people can easily download and use. It would be nice to have this and to have it linked from the web site. 

### Enhanced placement and documentation for Mininet examples

**Brief explanation**: Additionally, people frequently seem to be unaware of the examples and/or have trouble understanding them. It would be nice to document them and to place them more prominently in the documentation and/or on the web site. Eventually it woud be nice to have a sequence of tutorial examples, not just for Mininet but for SDN and Openflow in general

### Cluster mode - supporting execution over multiple machines (RemoteSwitch,L2TP/VDE/VXLAN/Capsulator, etc.)

**Brief explanation**: This is probably the biggest missing feature of Mininet, and something which was planned since day one but never actually completed. We would like to be able to create very large Mininet networks by using a cluster of machines. This means we need a way to easily start up Mininet instances on multiple machines and to wire them together into a larger simulation. This should be as transparent as possible so that the cluster just appears to be a larger machine running Mininet.  (Of course there is no longer a single clock domain, and other differences may leak through, but in general the same code should run on a single machine or on a cluster.)

### Hybrid network support (API for hardware, virtual and combo network tests)

### Seamless migration between Mininet and hardware

### Mininet control of real hardware

**Brief explanation**: These three features are probably variants of the same feature. Basically we would like to be able to integrate hardware components into a Mininet network and control both hardware and virtual components via the same standard Mininet API. Seamless migration means that the same setup scripts should be usable whether Mininet or a hardware testbed are being used. Note that Emulab and ns-2 are a bit ahead of us in that regard in that v-Emulab, hardware Emulab, and ns-2 all use the same basic Tcl script format for setting up experiments. (However, those scripts are not generally usable on hardware which isn't Emulab.)

### Automated deployment on EC2

**Brief explanation**: This is similar to the above - it would be nice to be able to spawn a simulation across multiple EC2 nodes semi-automatically.

### Automated deployment on other testbeds? (emulab, geni, etc.)

**Brief explanation**: Since there are already a number of provisioning APIs (including Emulab, GENI/FOAM, and OpenStack), perhaps it would be nice to ease migration between Mininet and those hardware testbeds (or a simple cluster of PCs with Ubuntu or another standard Linux distribution and no special software installed.)

### Automated creation of virtual network based on real network

**Brief explanation**: It would be quite neat to be able to discover the topology of a physical, hardware network and to recreate it automatically in Mininet.

### Mininet network debugger (ndb)?

**Brief explanation**: NetSight/ndb were built on top of Mininet. If we want to create an SDN SDK, it would be nice to make it as easy as possible to install and run ndb.

### Code refactoring including Mininet core which could be used independently

**Brief explanation**: The core of Mininet is very simple, but it has become more complicated as we have added more features to it. As a reaction to this, one Mininet user created Piconet, which is reminiscent of the original Mininet. But, it lacks many of the good features that we know and love about Mininet (one command line to rule them all, parametrized topologies, etc..) It would be nice to make the Mininet design more modular so that you could have a simplified core/microkernel of Mininet which could be used independently, along with modules which you could add as desired.

### Error recovery using Python's with statement (Mininet 3.0?)

**Brief explanation**: Another excellent design decision in Piconet was to use Python's with statement for automatic recovery and cleanup. We should adopt this as well. Although it would require API changes, it could make the code smaller and more reliable. Ideally we would not need mn -c to clean up state after Mininet was interrupted or a script crashed.

### Measured scalability results (and possibly improved scalability)

**Brief explanation**: Mininet's original goal was "1000 nodes on your laptop" but such networks aren't really practical. There's no reason for this - the problem is that the existing implementation has too much overhead. We should measure scalability, profile the system, and identify possible ways of improving scalability. Notably, 1000-node networks are very slow to start up, in part because veth pair creation is slow and adding interfaces to OVS switches gets slower and slower the more switches you have!!

### Provisioning advice and/or automatic provisioning support

**Brief explanation**: Students in CS244 ran into problems when they overcommitted the system. Currently we don't provide much in the way of provisioning guidance, warnings, or monitoring to determine when the system is overcommitted. It would be nice to do all three! Ideally the system could be run in a performance-accurate mode where it would try to guarantee that it was provisioned correctly and provide some kind of alarm or warning when things were going bad. Which leads us to the following....

### Integrated (emulator and emulated) performance monitoring

**Brief explanation**: We developed some performance monitoring code and tools for Mininet 2.0, CS244 and the CoNEXT paper, but these are in a separate source tree and are not currently integrated into the code base. What can we do to add monitoring code to guarantee that important emulation invariants are not being violated, and also to monitor both the performance of the emulator itself and the emulated system?
Performance accuracy benchmarks and evaluation

### Emulators: can we trust them?

**Brief explanation**: We have some benchmarks, but we don't really have a definitive answer as to how far Mininet results match reality. Ideally we would have a deep understanding of Mininet's performance accuracy, a thorough evaluation and comparison to hardware, and validation both against hardware and against invariants that should hold during emulation.

### Other OS support: Debian Wheezy, Fedora Core, BSD? OS X? Windows?

**Brief explanation**: .deb and .rpm install images in addition to PPA, and possibly versions for BSD, OS X, Windows???

Right now we're strongly in the Ubuntu camp. We can almost run out of the box on Debian Wheezy. Fedora Core installation would require modification to the install script. BSD is actually possible now that it has L2 virtual Ethernet and Open vSwitch. Perhaps even OS X is a possibility, although the requisite BSD subsystems (see netmap and VALE) would need to be ported somehow since I don't think they're included in the Xnu/Darwin kernel by default. 

### Link (e.g. wire or wireless) simulator support

**Brief explanation**: It would be nice to have a link emulator subclass of Link and at least one link simulator, even a simple C (or Python) program that simply does packet forwarding with delay and loss. Ideally we could support a simple wireless link emulator.

### Ability to more compactly package Mininet networks and download into VM

**Brief explanation**: It would be nice to be able to compactly package Mininet experiments without requiring a whole VM. On the other hand, VMs are guaranteed to work! There is a system (whose name I forget) on Linux which packages up applications with their libraries and support files in such a way as to be independent of the underlying OS. Debian packages are similar. Perhaps we could figure out a compact way to package up a whole Mininet system.

### "Pure" Python implementation (need to determine the performance hit)

**Brief explanation**: Although Mininet cannot truly be implemented in pure Python (it depends on features in the underlying OS), we can rewrite mnexec as Python code (this is what Piconet did as well.) However, we should measure the performance of mnexec vs. Python implementation of same.

### Enhanced unit tests

**Brief explanation**: Right now we don't have a whole lot in terms of unit tests. Ideally we should test all API calls and get some degree of coverage for all of Mininet.

### Enhanced system tests

**Brief explanation**: Mininet is great for system-level testing, but ironically we only have a couple of system-level tests that we use to test Mininet itself!! We should test all of our topologies, our switch classes, and various other subsystems like the CLI, command-line argument parsing, performance isolation, and cluster mode.

### Automatic testing of examples/

**Brief explanation**: Not only do we want to be confident that changes to Mininet don't break our examples, but the examples themselves can be thought of as system-level tests which can and should be integrate into automated testing.

### Performance analysis and fixes to the Linux kernel and Open vSwitch

**Brief explanation**: We really haven't adequately profiled the whole system, and it's tricky to do so. I expect there are some low-hanging fruit in both Linux and OVS which could be tackled to greatly improve Mininet's performance. In particular, I'd like to see (much) faster startup for large networks.

### Better Linux kernel support for emulator event scheduling (SCHED_MN)

**Brief explanation**: The Linux scheduler isn't really meant to do what we're using it for. I believe that we may be able to develop a scheduler which is closer to an event-driven simulation schedule in terms of making things happen when they should, for example guaranteeing that network invariants hold and that packets are delivered in the correct temporal order across ports, not just along single paths.

### Virtual time via time-dilation

**Brief explanation**: What sets Emulation apart from Simulation is that usually Emulation runs in wall-clock time rather than having a strong notion of virtual time. Time Dilation is a strategy to allow the emulator's notion of time to progress more slowly than real time, allowing for faster links and computational elements to be simulated than can be simulated running in actual real time. This could enable larger networks to be simulated, or faster components (e.g. 100 GBps interfaces or 10 GHz processors.)

### Virtual time via barrier synchronization

**Brief explanation**: Another approach to virtual time is to allow individual hosts to have separate notions of virtual time, and to pause or slow down hosts which are executing too quickly to allow slower hosts to catch up. Why is this potentially better than time dilation? Because it allows us to arbitrarily instrument certain hosts with time-consuming monitoring or other processing which might slow them down relative to the rest of the system. Note however that the synchronization is approximate - we cannot, for example, synchronize after every instructione (at least not without absurdly impractical overhead.)

### Different modes of operation to trade of emulation speed vs. performance accuracy

**Brief explanation**: Speed vs. detail is a classical trade-off in emulators and simulators. One question is: can we make Mininet faster by sacrificing some performance accuracy. Another is: can we sacrifice some emulation speed to gain more performance accuracy. Another is: can we improve both simulator performance and performance accuracy at the same time?

### Higher-performing switches (e.g. VALE-enabled OVS and/or custom switch)

**Brief explanation**: OVS is slow. VALE, in comparison, gets 70 GBps (!!!!) of switching bandwidth on a 1 GHz system. We should be able to do much, much better than we are currently doing, possibly by using the VALE-enabled OVS or creating a custom switch (megaswitch) which is optimized for performance and lives in a single address space. We might be able to trade off some emulation accuracy for performance as welll (e.g. simplified switch vs. OVS, shared memory vs. veth pairs, etc..) Note that VALE runs on both BSD and Linux, but requires a custom Linux kernel. Note that the BSD jail enhancements open up the intriguing possibility of Mininet on BSD and/or OS X (with additional work - see above.)

### Support for private /etc directory and possbly private filesystem, user space, PID space, etc.

**Brief explanation**: Although lightweight virtualization is one of Mininet's most compelling features, sometimes it is useful for hosts to have more private data. One example is programs like apache or sshd which expect to have their configuration in /etc. It should be relatively easily to allow hosts to have private /etc directories by using bind mounts, but getting this to work in a convenient and semi-automatic manner may be tricky. Additionally, it may be desirable to allow Mininet  hosts to have private user and PID spaces (although shared user and PID spaces are obviously quite convenient as well.) It should not be terribly difficult to allow these options to be configured both in Mininet() and via the command line.