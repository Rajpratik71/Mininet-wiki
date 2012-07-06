<!-- %META:TOPICINFO{author="BobLantz" date="1305074030" format="1.1" version="1.30"}% -->
<!-- %META:TOPICPARENT{name="MininetVMCreationNotes"}% -->
<!-- Use our custom page layout:
* Set VIEW_TEMPLATE = [MininetView](MininetView.html)
-->

- - -++Mininet Walkthrough

This walkthrough demonstrates most Mininet commands, plus its typical usage in concert with the Wireshark dissector.

It assumes that your base system is the Mininet VM, or a native install with all !OpenFlow tools and Mininet installed.

The entire walkthrough should take under an hour.

<!-- %TOC% -->


Everyday Usage
---------------


### Display Options

Display startup options:
<verbatim>
sudo mn -h</verbatim>

This walkthrough will cover typical usage of the majority of options listed.


### Start Wireshark

To view control traffic using the !OpenFlow Wireshark dissector, first open wireshark in the background:
<verbatim>
sudo wireshark &</verbatim>

In the Wireshark filter box, enter this filter, then click 'Apply':
<verbatim>
of</verbatim>

In Wireshark, click Capture, then Interfaces, then select Start on the loopback interface (lo).

For now, there should be no !OpenFlow packets displayed in the main window.


### Interact with Hosts and Switches

Start a minimal topology and enter the CLI:
<verbatim>
sudo mn</verbatim>

The default topology is minimal, which includes one !OpenFlow kernel switch connected to two hosts, plus the !OpenFlow reference controller. Other ones are available; see the --topo section in the output of 'mn -h'.

All four entities (2 host processes, 1 switch process, 1 reference controller) are now running in the VM. The controller can be outside the VM, and instructions for that are at the bottom.

If no specific test is passed as a parameter, the Mininet CLI comes up.

In the Wireshark window, you should see the kernel switch connect to the reference controller.

Display CLI commands:
<verbatim>
help</verbatim>

Display nodes:
<verbatim>
nodes</verbatim>

Display links:
<verbatim>
net</verbatim>

Dump information about all nodes:
<verbatim>
dump</verbatim>

You should see the switch and two hosts listed.

If the first string typed into the Mininet CLI is a host, switch or controller name, the command is executed on that node. Run a command on a host process:
<verbatim>
h2 ifconfig -a</verbatim>

You should see the host's h2-eth0 and loopback (lo) interfaces. Note that this interface (h2-eth0) is not seen by the primary Linux system when ifconfig is run, because it is specific to the network namespace of the host process. Also note that because this interface is half of a virtual ethernet pair, the host cannot ping itself from the interface.

In contrast, the switch by default runs in the root network namespace, so running a command on the "switch" is the same as running it from a regular terminal:
<verbatim>
s1 ifconfig -a</verbatim>

This will show the switch interfaces, plus the VM's connection out (eth0).

For other examples highlighting that the hosts have isolated network state, run 'arp' and 'route' on both s1 and h2.

It would be possible to place every host, switch and controller in its own isolated network namespace, but there's no real advantage to doing so, unless you want to replicate a complex multiple-controller network. Mininet does support this; see the --innamespace option.

Note that _only_ the network is virtualized; each host process sees the same set of processes and directories. For example, print the process list from a host process:
<verbatim>
h2 ps -a</verbatim>

This should be the exact same as that seen by the root network namespace:
<verbatim>
s1 ps -a</verbatim>

It would be possible to use separate process spaces with Linux containers, but currently Mininet doesn't do that. Having everything run in the "root" process namespace is convenient for debugging, because it allows you to see all of the processes from the console using ps, kill, etc.


### Verify Ping

Now, verify that you can ping from host 0 to host 1:
<verbatim>
h2 ping -c 1 h3</verbatim>

If a string appears later in the command with a node name, that node name is replaced by its IP address; this happened for h3.

You should see !OpenFlow control traffic. The first host ARPs for the MAC address of the second, which causes a packet_in message to go to the controller. The controller then broadcasts a packet_out message to other ports on the switch (in this example, the only other data port). The second host sees the ARP request and sends a broadcast reply. This reply goes to the controller, which sends it to the first host and pushes down a flow entry.

Now the first host knows the IP address of the second, and can send its ping via an ICMP Echo Request. This request, along with its corresponding reply from the second host, both go the controller and result in a flow entry pushed down (along with the actual packets getting sent out).

Repeat the last ping:
<verbatim>
h2 ping -c 1 h3</verbatim>

You should see a much lower ping time for the second try (&lt;100us). A flow entry covering ICMP ping traffic was previously installed in the switch, so no control traffic was generated, and the packets immediately pass through the switch.

An easier way to run this test is to use the Mininet CLI built-in pingall command, which does an all-pairs ping:
<verbatim>
pingall</verbatim>

Exit the CLI:
<verbatim>
exit</verbatim>


### Cleanup

If Mininet crashes for some reason, clean it up:
<verbatim>
sudo mn -c</verbatim>


Program Startup Options
------------------------


### Run a Regression Test

You don't need to drop into the CLI; Mininet can also be used to run self-contained regression tests.

Run a regression test:
<verbatim>
sudo mn --test pingpair</verbatim>

This command created a minimal topology, started up the !OpenFlow reference controller, ran an all-pairs-ping test, and tore down both the topology and the controller.

Another useful test is iperf (give it about 10 seconds to complete):
<verbatim>
sudo mn --test iperf</verbatim>

This command created the same Mininet, ran an iperf server on one host, ran an iperf client on the second host, and parsed the bandwidth achieved.


### Topology Variations

The default topology is a single switch connected to two hosts. You could change this to a different topo with --topo, and pass parameters for that topology's creation. For example, to verify all-pairs ping connectivity with one switch and three hosts:

Run a regression test:
<verbatim>
sudo mn --test pingall --topo single,3</verbatim>

Another example, with a linear topology (where each switch has one host, and all switches connect in a line):
<verbatim>
sudo mn --test pingall --topo linear,4</verbatim>


### Adjustable Verbosity

The default verbosity level is info, which prints what Mininet is doing during startup and teardown. Compare this with the full debug output with the -v param:

	sudo mn -v debug
	exit

Lots of extra detail will print out. Now try output, a setting that prints CLI output and little else:

	sudo mn -v output
	exit

Outside the CLI, other verbosity levels can be used, such as warning, which is used with the regression tests to hide unneeded function output.


### Custom Topologies

Custom topologies can be easily defined as well, using a simple Python API, and an example is provided in custom/topo-2sw-2host.py. This example connects two switches directly, with a single host off each switch:

%CODE{ lang="python" }% """Custom topology example

author: Brandon Heller (brandonh@stanford.edu)

Two directly connected switches plus a host for each switch:

host --- switch --- switch --- host

Adding the 'topos' dict with a key/value pair to generate our newly defined topology enables one to pass in '--topo=mytopo' from the command line. """

from mininet.topo import Topo, Node

class [MyTopo](MyTopo.html)( Topo ):
    "Simple topology example."

    def *__init__*( self, enable_all = True ):
        "Create custom topo."

        # Add default members to class.
        super( [MyTopo](MyTopo.html), self ).__init__()

        # Set Node IDs for hosts and switches
        leftHost = 1
        leftSwitch = 2
        rightSwitch = 3
        rightHost = 4

        # Add nodes
        self.add_node( leftSwitch, Node( is_switch=True ) )
        self.add_node( rightSwitch, Node( is_switch=True ) )
        self.add_node( leftHost, Node( is_switch=False ) )
        self.add_node( rightHost, Node( is_switch=False ) )

        # Add edges
        self.add_edge( leftHost, leftSwitch )
        self.add_edge( leftSwitch, rightSwitch )
        self.add_edge( rightSwitch, rightHost )

        # Consider all switches and hosts 'on'
        self.enable_all()

topos = { 'mytopo': ( lambda: [MyTopo](MyTopo.html)() ) } 
%ENDCODE%

When a custom mininet file is provided, it can add new topologies, switch types, and tests to the command-line. For example:
<verbatim>
sudo mn --custom ~/mininet/custom/topo-2sw-2host.py --topo mytopo --test pingall</verbatim>


### ID = MAC

By default, hosts and switches start with randomly assigned MAC addresses. This can make debugging tough, because every time the Mininet is created, the MACs change, so correlating control traffic with specific hosts is tough.

The --mac option is super-useful, and sets the switch MAC and host MAC and IP addrs to small, unique, easy-to-read IDs.

Before:
<verbatim>
mininet@mininet:~/mininet$ sudo mn
...
mininet> h2 ifconfig
h2-eth0  Link encap:Ethernet  HWaddr f6:9d:5a:7f:41:42  
          inet addr:10.0.0.2  Bcast:10.255.255.255  Mask:255.0.0.0
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:6 errors:0 dropped:0 overruns:0 frame:0
          TX packets:6 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:392 (392.0 B)  TX bytes:392 (392.0 B)
mininet> exit</verbatim>

After:
<verbatim>
mininet@mininet:~/mininet$ sudo mn --mac
...
mininet> h2 ifconfig
h2-eth0  Link encap:Ethernet  HWaddr 00:00:00:00:00:02  
          inet addr:10.0.0.2  Bcast:10.255.255.255  Mask:255.0.0.0
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)
mininet> exit</verbatim>


### XTerm Display

For more complex debugging, you can start Mininet so that it spawns one or more xterms.

To start an xterm for every host and switch, pass the -x option:
<verbatim>
sudo mn -x</verbatim>

After a second, the xterms will pop up, with automatically set window names.

Alternately, you can bring up additional xterms as shown below.

By default, only the hosts are put in a separate namespace; the window for each switch is unnecessary (that is, equivalent to a regular terminal), but can be a convenient place to run and leave up switch debug commands, such as flow counter dumps.

Xterms are also useful for running interactive commands that you may need to cancel, for which you'd like to see the output.

For example:

In the xterm labeled "switch: s1 (root)", run:
<verbatim>
dpctl dump-flows tcp:127.0.0.1:6634</verbatim>

Nothing will print out; the switch has no flows added.  To use dpctl with other switches, start up mininet in verbose mode and look at the passive listening ports for the switches when they're created.

Now, in the xterm labeled "host: h2", run:
<verbatim>
ping 10.0.0.3</verbatim>

Go back to s1 and dump the flows:
<verbatim>
dpctl dump-flows tcp:127.0.0.1:6634</verbatim>

You should see multiple flow entries now.

You can tell whether an xterm is in the root namespace by checking ifconfig; if all interfaces are shown (including eth0), it's in the root namespace. Additionally, its title should contain "(root)".

Close the setup, from the Mininet CLI:
<verbatim>
exit</verbatim>

The xterms should automatically close.


### Other Switch Types

Other switch types can be used. For example, to run the user-space switch:
<verbatim>
sudo mn --switch user --test iperf</verbatim>

Note the much lower TCP iperf-reported bandwidth compared to that seen earlier with the kernel switch.

If you do the ping test shown earlier, you should notice a much higher delay ping delay, since now packets must endure additional kernel-to-user-space transitions. The ping time will be more variable, as the user-space process representing the host may be scheduled in and out by the OS.

On the other hand, the user-space switch can be a great starting point for implementing new functionality, especially where software performance is not critical (such as when the user-space switch is controlling a hardware switch).

Another example switch type is Open vSwitch (OVS), which comes preinstalled on the Mininet VM. The iperf-reported TCP bandwidth should be similar to the !OpenFlow kernel module, and possibly faster:
<verbatim>
sudo mn --switch ovsk --test iperf</verbatim>


### NOX

To run a regression test with NOX running pyswitch, the NOX_CORE_DIR env var must be set to the directory containing the NOX executable. This is set on the mininet VM by default.

First verify that NOX runs:
<verbatim>
cd $NOX_CORE_DIR
./nox_core -v -i ptcp:</verbatim>

Ctrl-C to kill NOX, then run a test with NOX pyswitch:
<verbatim>
cd
sudo -E mn --controller nox_pysw --test pingpair</verbatim>

There's a hesitation of a few seconds while NOX loads and the switch connects, but then it should complete the ping.

Note that this time, mn was called via sudo -E, to keep the NOX_CORE_DIR environment variable.  If you're running nox remotely, using --controller remote, then the -E isn't necessary.  Alternately, you can change the first line of /etc/sudoers, via 'sudo visudo', to change the env_reset line to:

	Defaults !env_reset

... so that when running sudo the env var setup isn't changed.


### Mininet Benchmark

To record the time to setup and teardown a topology, use test 'none':

	sudo mn --test none


### Everything in its own Namespace (currently broken - ignore)

By default, the hosts are put in their own namespace, while switches and the controller are in the root namespace. To put every node in its own namespace, pass the --innamespace option:

	sudo mn --innamespace --switch user

Instead of using loopback, the switches will talk to the controller through a separately bridged control connection. By itself, this option is not terribly useful, but it does provide an example of how to isolate different switches.

	exit


Command-Line Interface Commands
--------------------------------


### Display Options

To see the list of Command-Line Interface (CLI) options, start up a minimal topology and leave it running. Build the Mininet:

	sudo mn

Display the options:

	help


### Python Interpreter

If the first phrase on the Mininiet command line is 'py', then that command is executed with Python. This might be useful for extending Mininet, as well as probing its inner workings. Each host, switch, and controller has an associated Node object.

At the Mininet CLI, run:

	py 'hello ' + 'world'

Print the accessible local variables:

	py locals()

Next, see the methods and properties available for a node, using the dir() function:

	py dir(s1)

You can read the on-line documentation for methods available on a node by using the help() function:

	py help(h2)
(Press "q" to quit reading the documentation.)

You can also evaluate methods of variables:

	py h2.IP()


### Link [Up/Down](Up/Down.html)

For fault tolerance testing, it can be helpful to bring links up and down.

To disable both halves of a virtual ethernet pair:

	link s1 h2 down

You should see an [OpenFlow](OpenFlow.html) Port Status Change notification get generated. To bring the link back up:

	link s1 h2 up


### XTerm Display

To display an xterm for h2 and h3:

	xterm h2 h3


Examples
---------

The examples directory in the Mininet source tree includes examples of how to use Mininet's Python API, as well as potentially useful code that has not been integrated into the main code base.


### SSH daemon per host

One example that may be particularly useful runs an SSH daemon on every host:

	sudo ~/mininet/examples/sshd.py

From another terminal, you can ssh into any host and run interactive commands:

	ssh 10.0.0.2
	ping 10.0.0.3 
	# press ctrl-c
	exit

Exit SSH example mininet:

	exit


Walkthrough Complete
---------------------

Congrats! You've completed the Mininet Walkthrough. Feel free to try out new topologies and controllers or check out the source code.

An example on how to use a remote controller is below.


Optional
---------

These are not required, but you might find them useful to skim.


### Remote Controller

Note: this step is not part of the default walkthrough; only attempt if you have a controller running outside of the VM, such as on the VM host, or a different physical PC.

When you start a Mininet, each switch can be connected to a remote controller - which could be outside the VM and on your machine, or anywhere in the world.

This setup may be convenient if you already have a custom version of NOX w/dev tools (such as Eclipse) installed on the local machine, or you want to test a controller running on a different physical machine (maybe even in the cloud).

If you want to try this, fill in the host IP and/or listening port.
<verbatim>
sudo mn --controller=remote --ip=[controller IP] --port=[controller listening port]</verbatim>
