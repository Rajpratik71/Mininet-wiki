## Introduction

### ns-3 emulation features

[ns-3](http://www.nsnam.org/) network simulator has ability to work in so called [real-time/emulation](http://www.nsnam.org/wiki/index.php/Emulation_and_Realtime_Scheduler) mode. In that mode, the simulator can exchange packets in real-time with the outside world. It means, that packets originating from simulated nodes can be processed by a real network. Another possibility is to drive a simulated network with packets from real nodes. These two options are covered by two different kinds of ns-3 modules: _FdNetDevice_ and _TapBridge_.

See more: [Realtime](http://www.nsnam.org/docs/release/3.17/manual/singlehtml/index.html#realtime) and [Emulation Overview](http://www.nsnam.org/docs/release/3.17/models/singlehtml/index.html#emulation-overview).

[_FdNetDevice_](http://www.nsnam.org/docs/release/3.17/models/singlehtml/index.html#file-descriptor-netdevice) can read and write from a file descriptor, which can be associated to a network device (via raw socket). This allows ns-3 simulations to read frames from and write frames to a network device on the host. Instead of an ns-3 channel connecting ns-3 nodes, real hardware provided by the testbed can be used. This allows ns-3 applications and protocol stacks attached to a simulation node to communicate over real hardware. The primary use for this configuration is to generate repeatable experimental results in a real-world network environment that includes all of the ns-3 tracing, logging, visualization and statistics gathering tools.

    FdNetDevice case. Nodes: simulated. Network: real.

    +----------------------+     +-----------------------+
    |         host 1       |     |         host 2        |
    +----------------------+     +-----------------------+
    |    ns-3 simulation   |     |                       |
    +----------------------+     |         Linux         |
    |       ns-3 Node      |     |     Network Stack     |
    |  +----------------+  |     |   +----------------+  |
    |  |    ns-3 TCP    |  |     |   |       TCP      |  |
    |  +----------------+  |     |   +----------------+  |
    |  |    ns-3 IP     |  |     |   |       IP       |  |
    |  +----------------+  |     |   +----------------+  |
    |  |   FdNetDevice  |  |     |   |                |  |
    |  |    10.1.1.1    |  |     |   |                |  |
    |  +----------------+  |     |   +    ETHERNET    +  |
    |  |   raw socket   |  |     |   |                |  |
    |--+----------------+--|     |   +----------------+  |
    |       | eth0 |       |     |        | eth0 |       |
    +-------+------+-------+     +--------+------+-------+
           10.1.1.11                     10.1.1.12
               |       real network         |
               +----------------------------+

[_TapBridge_](http://www.nsnam.org/docs/release/3.17/models/singlehtml/index.html#tap-netdevice) allows a real host to participate in an ns-3 simulation as if it were one of the simulated nodes. It can be viewed as essentially an inverse configuration to the previous one. It allows host systems and virtual machines running native applications and protocol stacks to integrate with a ns-3 simulation. In this case ns-3 simulator connects to a TAP virtual interface created on Linux host. Packets send by host to the TAP device are transmitted through the file descriptor to the ns-3 process. Next they are forwarded down by _TapBridge_ to the ns-3 net device and transmitted over the ns-3 emulated channel. The typical use case for this environment is to analyse the behaviour of native applications and protocol suites in the presence of large simulated ns-3 network. 

    TapBridge case. Nodes: real. Network: simulated.

    +--------+
    |  Linux |
    |  host  |                    +----------+
    | ------ |                    |   ghost  |
    |  apps  |                    |   node   |
    | ------ |                    | -------- |
    |  stack |                    |          |     +----------+
    | ------ |                    |          |     |   node   |
    |  TAP   |                    |==========|     | -------- |
    | device | <----- IPC ------> |   tap    |     |    IP    |
    +--------+                    |  bridge  |     |   stack  |
                                  | -------- |     | -------- |
                                  |   ns-3   |     |   ns-3   |
                                  |   net    |     |   net    |
                                  |  device  |     |  device  |
                                  +----------+     +----------+
                                       ||               ||
                                  +---------------------------+
                                  |        ns-3 channel       |
                                  +---------------------------+


### Link simulation

Two _TapBridge_ net devices can be used to create emulated link between two TAP virtual interfaces. Two ghost nodes inside ns-3 need to be created. Each node should consist of a _TapBridge_ and ns-3 net device. These net devices should be connected together by an emulated ns-3 channel. Each _TapBridge_ should be connected to the proper TAP interface. These TAP interfaces can serve now as the endpoints of the emulated link.

    +--------+                                         +--------+
    |  name  |                                         |  name  |
    | space1 |                                         | space2 |
    | ------ |                                         |--------|
    |  node  |                                         |  node  |
    | ------ |                                         |--------|
    |  stack |                                         |  stack |
    | ------ |                                         |--------|
    |  TAP   |      |==========|     |==========|      |  TAP   |
    | device |<-fd->|   tap    |     |   tap    |<-fd->| device |
    +--------+      |  bridge  |     |  bridge  |      +--------+
                    | -------- |     | -------- |
                    |   ns-3   |     |   ns-3   |
                    |   net    |     |   net    |
                    |  device  |     |  device  |
                    +----------+     +----------+
                         ||               ||
                    +---------------------------+
                    |        ns-3 channel       |
                    +---------------------------+
                 |<------------------------------->|
                             ns-3 process

