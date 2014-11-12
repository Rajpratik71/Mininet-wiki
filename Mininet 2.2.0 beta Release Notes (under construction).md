[ Additional details on the 2.2.0 release will be available in the
release announcement (TBD) and the README (TBD) file. ]

This document contains supplementary information and issues not included in that file.

### Documentation for Examples and Tests

The examples have Python docstring documentation, and can be used as modules, e.g.

    sudo python -m mininet.examples.nat

Their components may also be imported, but they should be considered volatile and not part of the official Mininet API. Since they're not part of the official API, their documentation is also not currently included at [api.mininet.org](api.mininet.org) or generated with `make doc`. However, you may wish to look at the code and `README.md` file in the [examples/](https://github.com/mininet/mininet/tree/2.2.0b1/examples) directory.

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

### Cluster Edition Prototype

* Because of issues with `ssh` multiplexing, we do not recommend using it at this time.

* Additional information may be found on the [[Cluster Edition Prototype]] wiki page.

### Errata

* Currently `--switch user` cannot be used with `--mac`; there appears to be a bug where the reference switch (and presumably its derivates like the CPqD switch) does not correctly match and forward packets when the MAC address is `00:00:00:00:00:01`.

* Currently `--switch user` has VERY poor performance with Linux 3.13 kernels - about 1000x worse than earlier kernels - and should not be user for anything beyond simple connectivity tests. We hope this can be remedied in the future.

* Currently `--switch ivs` cannot be used with more than a few switches; a bug has been submitted to the IVS developers.

* Currently `--host rt` may not work correctly when installed with apt-get install on Ubuntu. The mininet package in Ubuntu is missing the cgroup-bin dependency. To fix this, install cgroup-bin.

* Currently `--host rt` will not work correctly with any default ubuntu kernel. The rt scheduler has been disabled by default in all ubuntu kernels. To fix this, enable RT_GROUP_SCHED in your kernel.

* Currently `NetworkManager` may attempt to assign leases to the interfaces from a Mininet instance. This will cause an unwanted flood of DHCP requests, and your cpu usage to max out when Mininet is idle. To prevent this, stop `NetworkManager` with `$ sudo stop network-manager`.