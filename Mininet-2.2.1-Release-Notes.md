[in development]

Mininet 2.2.1 will be primarily a performance enhancement release to Mininet 2.2.0.

Some important changes:

- By default, `OVSSwitch`es are now started up *and* shut down in batch operations, using `batchStartup()` and `batchShutdown()`

- links not added using `addLink()` will not be cleaned up automatically in `Mininet.stop()`. Note that `sshd.py` has changed slightly to reflect this. `controlnet.py` has also changed.

- `printPid` is now `False` by default in `Node.cmd()` and `Node.sendCmd()`

#### OVS Patch Links

An `OVSLink` (`--link ovs`) class has been added which implements OVS patch links. Unlike `veth` pairs, OVS Patch Links are virtual entities within OVS itself, and can potentially support much higher data rates (particularly over multiple links, probably due to OVS's flow rule optimization) as well as slightly faster startup time.

However, **OVSLinks have several serious limitations**, including the following:

1. OVS patch ports are not real Linux interfaces, so you cannot monitor them using `tcpdump` or `wireshark`.

2. OVS patch ports are not real Linux interfaces, so you cannot control their behavior using `tc`.

3. Because of the above, `OVSLink` does not support link modeling including bandwidth limits, delay modeling, or loss modeling.

4. Based on our experience so far, no more than ~64 `OVSLinks` should be used in a row. This means that a command like `mn --topo linear,64 --test iperf` will work, but `mn --topo linear,80 --test iperf` will fail.

That being said, if you want the fastest possible data rates on small-diameter networks, you may wish to try `OVSLink`s. If they don't work, just go back to regular links.
