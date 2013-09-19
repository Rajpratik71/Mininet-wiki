For details on the content and new features of 2.1.0, please consult the [README] (https://github.com/mininet/mininet/blob/2.1.0rc2/README.md) file. This document contains supplementary information and issues not included in that file.

### Documentation for Examples and Tests

The examples have Python docstring documentation, and can be used as modules, e.g.

    sudo python -m mininet.examples.nat

Their components may also be imported, but they should be considered volatile and not part of the official Mininet API. Their documentation is also not currently included at [api.mininet.org](api.mininet.org) or generated with `make doc`. However, you may wish to look at the code and `README.md` file in the [examples/](https://github.com/mininet/mininet/tree/2.1.0rc2/examples) directory.

The tests are not included in the Mininet `.egg`, nor are they documented at [api.mininet.org](api.mininet.org), but they are included in the Mininet source tree.

### Potential VirtualBox Issues

* VirtualBox may complain about missing hardware on initial import - you may safely ignore this message.

* We've added a symlink to the top-level examples directory from the mininet code directory to make examples importable. If you are using VirtualBox and clone the repository to a shared folder, the symlink will be broken. This is because VirtualBox does not allow guests to create symlinks in shared folders by default for security reasons. To enable symlink creation, you should do the following:
    - Shut down your VM and quit the VirtualBox Manager
    - Run the following command on your host OS: 
        `VBoxManage setextradata VM_NAME`
        `VBoxInternal2/SharedFoldersEnableSymlinksCreate/SHARE_NAME 1`
    - Restart your VM

### Errata

* Currently one of the bandwidth limit tests in `test_hifi.py` fails (or sometimes fails) due to low bandwidth in OVS in user mode on Ubuntu 13.10/Saucy Salamander development builds (mid-September); if you run into this problem, the workaround is either to use kernel-mode OVS or an earlier Ubuntu release.
 
* Currently `--switch user` cannot be used with `--mac`; there appears to be a bug where it does not correctly match and forward packets when the MAC address is `00:00:00:00:00:01`.

* Currently `--switch ivs` cannot be used with more than a few switches; a bug has been submitted to the IVS developers.

* Currently `--host rt` may not work correctly on multiprocessors (use `--host cfs` instead, or run on a single processor.)

* There is currently an annoying bug with certain kernels which may result in messages similar to the following:

        [  176.965603] unregister_netdevice: waiting for h1-eth0 to become free. Usage count = 1
        [  185.508516] unregister_netdevice: waiting for lo to become free. Usage count = 2
        [  187.245149] unregister_netdevice: waiting for h1-eth0 to become free. Usage count = 1
        [  195.781125] unregister_netdevice: waiting for lo to become free. Usage count = 2
        [  197.521721] unregister_netdevice: waiting for h1-eth0 to become free. Usage count = 1
        [  206.053467] unregister_netdevice: waiting for lo to become free. Usage count = 2

   This bug has been reported on launchpad.net, and there is unfortunately no solution yet other than rebooting the VM and/or trying a different kernel (for example we have had good luck with `3.2.0-31-generic`.)

   This bug seems to happen more frequently with Ubuntu 12.04.3 (`3.8.0-29-generic` kernel), although it has also been seen with newer kernels in 13.10.