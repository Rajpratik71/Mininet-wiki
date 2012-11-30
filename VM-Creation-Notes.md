Creating a Mininet VM from Scratch
===================================

These instructions cover the process of building a Mininet-capable VM from scratch, using Ubuntu 12.10. For setup instructions, see [VM-Setup-Notes](VM-Setup-Notes).

_Note: If you need to build on a non-Ubuntu distro, you are currently on your own. The included shell scripts may work on your platform, but significant changes are likely (e.g. `yum` vs. `apt-get` and different package names for Fedora.)_

**First, create a new Ubuntu 12.10 VM** (using VMware, VirtualBox, etc..) We use user: `mininet` and password: `mininet` in our default VM, but any user/password combo should work as long as it has admin/sudo privileges.

**Next, log in to the new VM and do the following**:

    wget https://raw.github.com/mininet/mininet/master/util/vm/install-mininet-vm.sh
    time install-mininet-vm.sh

After this completes (about 5 minutes), Mininet should work:'

    $ sudo mn --test pingall

**That's it!**

For historical reference, we have also preserved the [Old VM Creation Notes](Old-VM-Creation-Notes).

### How we created the Mininet 2.0.0 VM Image

1. Created a new VM `mininet-vm` in VMware using the Ubuntu 12.10 server amd64 image with auto-install, and `mininet` for all user information and passwords.
2. Customized it by increasing the memory to 1536 MB.
3. Customized it by adding an additional private (host-only) network interface.
4. Booted VM and let auto-install complete


