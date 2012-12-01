Creating a Mininet VM from Scratch
===================================

These instructions cover the process of building a Mininet-capable VM from scratch, using Ubuntu 12.10. For setup instructions, see [VM-Setup-Notes](VM-Setup-Notes).

_Note: If you need to build on a non-Ubuntu distro, you are currently on your own. The included shell scripts may work on your platform, but significant changes are likely (e.g. `yum` vs. `apt-get` and different package names for Fedora.)_

**First, create a new Ubuntu 12.10 VM** (using VMware, VirtualBox, etc..) We use user: `mininet` and password: `mininet` in our default VM, but any user/password combo should work as long as it has admin/sudo privileges.

**Next, log in to the new VM and do the following**:

    wget https://raw.github.com/mininet/mininet/master/util/vm/install-mininet-vm.sh
    time bash install-mininet-vm.sh

After this completes (about 5 minutes), Mininet should work:

    $ sudo mn --test pingall

**That's it!**

For historical reference, we have also preserved the [Old VM Creation Notes](Old-VM-Creation-Notes).


***

### How we created the Mininet 2.0.0 VM Image

In the future, these steps will be automated, but for now there are several manual steps.

1. Created a new VM `mininet-vm` in VMware using the Ubuntu 12.10 server amd64 image with easy install, and `mininet` for all user information and passwords.

2. Customized it by increasing the memory to 1536 MB.

3. Booted VM and let easy install complete

4. Ran the following commands:

    `wget https://raw.github.com/mininet/mininet/master/util/vm/install-mininet-vm.sh`
    `time bash install-mininet-vm.sh`

    (Less than 4 minutes to complete.)

5. Tested mininet

    `sudo mn --test pingall`

6. Shut down and exported OVF

    `time /Applications/VMware\ OVF\ Tool/ovftool mininet-vm.vmx mininet-vm.ovf`

   Completed in 1:51

7. Fixed OVF so it will load in VirtualBox

    `sed -i -e '/vmw:Config/d' mininet-vm.ovf`

8. Stored SHA1 checksums in mininet-vm.mf

    `openssl sha1 mininet-vm.ovf mininet-vm-disk1 > mininet-vm.mf` (OS X command)

9. Zipped and uploaded to GitHub

    `mkdir mininet-ovf`  
    `mv mininet-vm-disk1.vmdk mininet-vm.{mf,ovf} mininet-ovf`  
    `zip -r mininet-ovf mininet-ovf`  
    `mv mininet-ovf.zip mininet-2.0.0-113012-amd64-ovf.zip`

----

I didn't do the following:

1. Removed DHCP leases

    `cd /var/lib/dhcp/`  
    `rm *lease*`

   This may not be the right thing to do, as it causes the VirtualBox VM boot to hang
   for 60+ seconds at "waiting for network configuration."

2. Shut down VM and added an additional private (host-only) network interface.

   This also didn't seem to work as the OVF was imported into VirtualBox with only one interface.

