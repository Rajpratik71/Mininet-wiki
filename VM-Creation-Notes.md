Creating a Mininet VM from Scratch
===================================

These instructions cover the process of building a Mininet-capable VM from scratch, using Ubuntu 12.10. After completing these setup instructions, see [VM-Setup-Notes](VM-Setup-Notes) to set up your VM.

_Note: If you need to build on a non-Ubuntu distro, you are currently on your own. The included shell scripts may work on your platform, but significant changes are likely (e.g. `yum` vs. `apt-get` and different package names for Fedora.)_

**First, create a new Ubuntu 12.10 VM** (using VMware, VirtualBox, etc..) 

To keep the VM smaller, use the server variant.  We use 64-bit images, and specifically `ubuntu-12.10-server-amd64.iso`, which can be downloaded from the [Ubuntu Quantal page](http://releases.ubuntu.com/quantal/).

The Mininet VM uses username: `mininet` and password: `mininet`, but any user/password combo should work as long as it has admin/sudo privileges.

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

2. Customized it by increasing the memory to 1536 MB and naming it `mininet-vm`

3. Booted `mininet-vm` and let easy install complete (~5min)

4. Ran the following commands (~4min):

        wget https://raw.github.com/mininet/mininet/master/util/vm/install-mininet-vm.sh
        time bash install-mininet-vm.sh
        sudo mn --test pingall

6. Shut down and ran the following (~3min):

   *Note: This assumes you're running on a Mac with the
    [VMware OVF Tool](http://www.vmware.com/support/developer/ovf/) installed!!*

        echo "*** Converting to OVF"
        time /Applications/VMware\ OVF\ Tool/ovftool mininet-vm.vmx mininet-vm.ovf
        echo "*** Fixing OVF so it works with VirtualBox" 
        sed -i -e '/vmw:Config/d' mininet-vm.ovf
        echo "*** Updating SHA1 checksums"
        openssl sha1 mininet-vm.ovf mininet-vm-disk1.vmdk > mininet-vm.mf
        echo "*** Moving OVF to its own directory"
        mkdir mininet-ovf
        mv mininet-vm-disk1.vmdk mininet-vm.{mf,ovf} mininet-ovf
        echo "*** Zipping OVF"
        zip -r mininet-ovf mininet-ovf
        mv mininet-ovf.zip mininet-2.0.0-113012-amd64-ovf.zip

7. Uploaded to <https://github.com/mininet/mininet/downloads> (~10min)

   File to upload: `mininet-ovf.zip mininet-2.0.0-113012-amd64-ovf.zip`  
   Short Description: `Mininet 2.0.0 VM - Ubuntu 12.10 server 64-bit - OVF - 11.30.12`  

   While I was waiting for the download, I extracted the zip archive and verified that I could
   import it and run in VirtualBox.

8. Downloaded from <https://github.com/mininet/mininet/downloads> (~10min)

9. Unzipped and imported into VirtualBox (~3min)

10. Booted, logged in, and tested with

        sudo mn --test pingall

11. Profit!

----

I didn't do the following:

1. Removed DHCP leases

        cd /var/lib/dhcp/
        rm *lease*

   This may not be the right thing to do, as it causes the VirtualBox VM boot to hang
   for 60+ seconds at "waiting for network configuration."

2. Shut down VM and added an additional private (host-only) network interface.

   This also didn't seem to work as the OVF was imported into VirtualBox with only one interface.