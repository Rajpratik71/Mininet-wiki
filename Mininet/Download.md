Download and Get Started with Mininet!
=======================================

The easiest way to get started is to **download a pre-packaged Ubuntu VM**. This VM includes a modified kernel, all OpenFlow binaries and tools pre-installed, plus tweaks to the kernel configuration to support larger Mininet networks.


Option 1: Mininet VM Installation (recommended)
------------------------------------------------
VM installation is the easiest and most foolproof way of installing Mininet, so it's what we recommend to start with.

Follow these steps for a VM install:

1. Download the Mininet VM image from https://github.com/downloads/mininet/mininet/mininet-vm-ubuntu11.10-052312.vmware.zip . This will take some time. It's ~800MB-1GB, compressed.

1. Download and install a virtualization system. We recommend [VirtualBox](http://www.virtualbox.org/wiki/Downloads) (free, GPL) because it is **free** and works on OS X, Windows, and Linux (though it's slightly slower than VMware in our tests.) You can also use [Qemu](http://qemu.org) for any platform, [VMware Workstation](http://www.vmware.com/products/workstation/) for Windows or Linux, or [VMware Fusion](http://www.vmware.com/products/fusion) for Mac, [KVM](http://www.linux-kvm.org) (free, GPL) for Linux.

1. Sign up for the `mininet-discuss` mailing list at https://mailman.stanford.edu/mailman/listinfo/mininet-discuss. This is the source for Mininet **support** and discussion with the friendly Mininet community. ;-)

1. Run through the [VM-Setup-Notes](VM-Setup-Notes) to log in to the VM and install Mininet code on it.

1. Follow the [Walkthrough](Walkthrough) to get familiar with Mininet commands and typical usage.

Once you've completed the [Walkthrough](Walkthrough), you should have a clear idea for what Mininet is and what you might use it for. If you are interested in OpenFlow and Software-Defined Networking, you will want to complete the [OpenFlow tutorial](http://www.openflow.org/wk/index.php/OpenFlow_Tutorial) as well. Good luck, and have fun!


Option 2: Native Mininet Installation on Ubuntu 11.10
------------------------------------------------------

If you want to install natively on Ubuntu 11.10 (or 10.04 LTS) the basic technique is:

    git clone git://github.com/mininet/mininet
    mininet/util/install.sh -a

This takes about 15-20 minutes and will install everything that is included in the Mininet VM, including NOX classic/destiny + tutorial code, the OpenFlow wireshark dissector, etc..

Alternately, it may be possible to install a "minimal" configuration using

    mininet/util/install.sh -fnv

This will install the reference OpenFlow switch and controller, Mininet and its dependencies, and Open vSwitch.
