Additional details on the 2.2.0 release are available in the
[release announcement](http://mininet.org/blog), the 
[README](https://github.com/mininet/mininet/tree/2.2.0/README.md) file,
and the [Mininet documentation](http://docs.mininet.org).

To download Mininet, visit http://mininet.org/download for pre-built
VM images and [github](https://github.com/mininet/mininet)
(or [code.mininet.org](http://code.mininet.org)) for the source code.

**This document contains supplementary information and issues.**

### Documentation for Examples and Tests

The examples have Python docstring documentation, and can be used as modules, e.g.

    sudo python -m mininet.examples.nat

Their components may also be imported, but they should be considered volatile and not part of the official Mininet API. Since they're not part of the official API, their documentation is also not currently included at [api.mininet.org](http://api.mininet.org) or generated with `make doc`. However, you may wish to look at the code and `README.md` file in the [examples/](https://github.com/mininet/mininet/tree/2.2.0rc1/examples) directory.

The tests are not included in the Mininet `.egg`, but they are provided in the Mininet source tree.

### Potential VirtualBox/VM Issues and Solutions

* If VirtualBox (or any other virtual machine monitor) cannot import the `.ovf` directly, try creating a new VM using the `.vmdk` file as its disk.

* VirtualBox may complain about missing hardware on initial import - you may safely ignore this message.

* We've added a symlink to the top-level examples directory from the mininet code directory to make examples importable. If you are using VirtualBox and clone the repository to a shared folder, the symlink will be broken. This is because VirtualBox does not allow guests to create symlinks in shared folders by default for security reasons. To enable symlink creation, you should do the following:
    - Shut down your VM and quit the VirtualBox Manager
    - Run the following command on your host OS: 
        `VBoxManage setextradata VM_NAME`
        `VBoxInternal2/SharedFoldersEnableSymlinksCreate/SHARE_NAME 1`
    - Restart your VM

### Test / Hi-Fi Issues

* Some performance tests (e.g. `test_linearbandwidth.py`) can occasionally fail in virtualized or non-quiescent environments due to performance variance, scheduling, or timing skew. Others may fail if they hit resource limits of the underlying system (e.g. a netbook or a VM with limited resources.)

* For the most accurate performance results, you may wish to run Mininet on a quiescent, non-virtualized environment.

### Cluster Edition Prototype issues

* We do not currently recommend enabling `ssh` multiplexing (`ControlPath` feature) as we have observed problems with it.

* Additional information may be found on the [[Cluster Edition Prototype]] wiki page.

### Errata

* Currently the Stanford Reference Switch (`--switch user`) and the CPqD switch (also `--switch user` if it is installed) do not correctly match MAC address `00:00:00:00:00:01`. This means that they will not work with `--mac` or `autoSetMacs=True`.

* The Stanford Reference Switch (`--switch user`) also currently has awful performance (kilobits per second rather than megabits) on certain recent Linux kernels, including the one that we ship in the Mininet VM. We hope this can be fixed in the future. In the mean time we recommend OVS or IVS unless you are modeling a dial-up modem or ISDN.

* Currently `NetworkManager` may attempt to assign leases to the interfaces from a Mininet instance. This will cause an unwanted flood of DHCP requests, and your cpu usage to max out when Mininet is idle. To prevent this, stop `NetworkManager` with `$ sudo stop network-manager`.

* IPv6 keeps coming back, even though we try to disable it! Although it's good that IPv6 is now correctly virtualized, this means that the Mininet VM does not now disable IPv6 globally as intended. This means that unless you disable IPv6 in each Mininet host, you may see IPv6's irritating neighbor discovery packets. On the up side, it is now easier to use IPv6 in Mininet.

* Currently `--host rt` may not work correctly when installed with apt-get install on Ubuntu 12.04.5. The `mininet` package is missing the `cgroup-bin` dependency. To fix this, `apt-get install cgroup-bin`.

* Currently `--host rt` will not work correctly with any default ubuntu kernel. The POSIX real-time scheduler has been disabled by default in all Ubuntu kernels. To fix this, enable `RT_GROUP_SCHED` in your kernel.





### Bug Reports and Support

You are encouraged to submit reports for any additional **bugs** you may
encounter via [github](https://github.com/mininet/mininet/issues),
and additional questions to the `mininet-discuss` mailing list. You may
also edit these notes to fix errors or provide clarification.