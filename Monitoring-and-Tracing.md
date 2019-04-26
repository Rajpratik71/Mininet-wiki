### Network Invariant Monitoring with Mininet

How do we verify that an emulator is correct without comparing
it directly against hardware?

Even if it matches hardware, how do we know that it is actually
doing the right thing?

[Heller13][1] suggests the very useful idea of *network invariants.*
Just as *loop invariants* must hold during every iteration of
a loop, or *function invariants* during every invocation of a
function, network invariants shoud hold for the duration of a
network experiment.

Network invariants give us a signal to detect when things
have gone obviously wrong in some way.

If the invariants do not hold, then we know something is
definitely wrong and we should not trust our experimental
results.

If the invariants do hold, then we don't know that our
experimental results are correct, but we will at least have
higher confidence in them since the invariants tell us that
there are know obvious problems.

### Some possible invariants to measure:

#### Link data rate

On a link that we expect to be saturated with full-sized packets,
the link data rate should be within an acceptable tolerance of
the configured link rate.

The obvious reason is that a faster or slower link rate means
that the link is not being accurately modeled: a faster rate
usually means rate limiting isn't working, and a slower rate
usually means that we are hitting resource limits.

We can measure this relatively easily using `bwm-ng`.

#### Packet spacing

On a link that we expect to be saturated with full-sized packets,
the packet spacing should be within an acceptable tolerance of
the expected spacing.

As per [Heller13][1] we suggest a maximum deviation of one packet -
basically allowing us to be one packet ahead or behind of where
we should be at any given time. This may be a generous constraint,
but it is clear that hardware could not overlap packet timing
intervals, and also that a delay of one packet time would cut
the link rate by 50%.

We can measure packet spacing easily using the Linux `ftrace`
(function tracing) subsystem and tracing `net:net_dev_xmit`
events for the interface we are interested in.

#### CPU Utilization

We want to make sure that virtual hosts are not using more than
their configured CPU utilization.

For hosts that we expect to be compute bound, we want to make
sure that they are in fact getting their desired CPU
utilization.

We can monitor cpu utilization of `cgroups` using the `cpuacct`
(CPU accounting controller) for `cgroups`.

#### RX-to-Schedule latency

Since we are multiplexing the underlying system, it is possible
that a packet will arrive when a process should be running on
a virtual host, but is currently descheduled.

When this happens, we want to make sure that the latency is
acceptable. If this is more than a packet time on a saturated
link, then the process may find it impossible to keep up with
incoming data.

This is trickier to monitor than other statistics and may be
more costly. We may be able to monitor it by tracing container
scheduling (which we can do by instrumenting sched_switch
with `bcc`/EBPF) and correlating it with packet received
events (which could be something like `net:netif_rx`, but I
should look into this more to be absolutely sure.)

#### Queue depth

If the hosts and application can saturate a bottleneck link,
this is likely to cause some queueing somewhere in the system.

We need to create a queue that we can monitor, and we do this
by using `tc`, which can also mark packets using ECN.
this gives us a pseudo-output-queued switch, though technically
it is the links that have queues, and there can be queuing
at the host end of the link as well.

If a switch egress port is oversubscribed, then we will expect
a queue to build there. (Presumably this is the queue which
we use for ECN.) We can sample the queue size using `tc -s`.

We should also be able to monitor dropped packets on the
interface.

#### TCP Tracing

For TCP `reno` over long periods of time we should see the
expected CWND sawtooth. There may be other values we should
look at for something like `cubic` or `bbr`.

We can also monitor other internal TCP state.

We can monitor TCP state with ftrace using the net:tcp_probe
event.

#### Full Packet Tracing

We can use `tcpdump`/`tshark`/`wireshark` to monitor the actual packets
transmitted over an interface and either decode them immediately or
save them for later analysis.

We may wish to analyze them to make sure that other constraints
are being maintained, such as packet spacing or data rate. We
can also verify that they are being marked properly with ECN.

We can also analyze the packets in a TCP session using
`tcptrace`.


### WARNING: Tracing is resource-intensive!!

It's easy to eat up a ton of resources (usually CPU but
sometimes memory and I/O as well) using tracing.

Because of this, tracing can perturb the experimental
results! If the results are already questionable, it can
make them much worse.

Taking a conservative approach and minimizing the amount
of tracing during an experiment should mitigate this
effect.

One approach to minimize the impact of monitoring is to
run an experiment repeatedly. We can run the experiment
a number of times without monitoring and verify that
a set of base measurements do not change.

Then for each invariant we can rerun the experiment
several times and verify that

1. The base measurements do not change, and

2. The invariant holds during the experiment



[1]:https://stacks.stanford.edu/file/druid:zk853sv3422/heller_thesis-augmented.pdf