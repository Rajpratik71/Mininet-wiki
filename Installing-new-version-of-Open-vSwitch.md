The following instruction show how to replace the version of Open vSwitch that comes in the Mininet VM.  This example shows how to upgrade to Open vSwitch 1.10, but the same basic instructions could be used to upgrade to any other version of Open vSwitch.

Become root:

    mininet@mininet-vm:~$ sudo -s

Remove old packages:

    root@mininet-vm:~# apt-get remove openvswitch-common openvswitch-datapath-dkms openvswitch-controller openvswitch-pki openvswitch-switch

Download and unpack OpenVSwitch 1.10:

    root@mininet-vm:~# cd /root
    root@mininet-vm:~# wget http://openvswitch.org/releases/openvswitch-1.10.0.tar.gz
    root@mininet-vm:~# tar zxvf openvswitch-1.10.0.tar.gz

Build and install:

    root@mininet-vm:~# cd openvswitch-1.10.0/
    root@mininet-vm:~# ./configure --prefix=/usr --with-linux=/lib/modules/`uname -r`/build
    root@mininet-vm:~# make
    root@mininet-vm:~# make install
    root@mininet-vm:~# make modules_install
    root@mininet-vm:~# rmmod openvswitch
    root@mininet-vm:~# depmod -a

Disable to default controller from starting at boot:

    root@mininet-vm:~# echo "manual" >> /etc/init/openvswitch-controller.override
    root@mininet-vm:~# /etc/init.d/openvswitch-controller stop


Start OVS server process:

    root@mininet-vm:~# /etc/init.d/openvswitch-switch start

That should be it.  Once you have finished, you can confirm that you have the latest Open vSwitch installed and the latest kernel module.  You should see something like the example below.

    root@mininet-vm:~# ovs-vsctl show
    a96f86e7-ca43-487f-b65d-e18b52a64330
        ovs_version: "1.10.0"

    root@mininet21:~# modinfo openvswitch
    filename:       /lib/modules/3.5.0-40-generic/extra/openvswitch.ko
    version:        1.10.0
    license:        GPL
    description:    Open vSwitch switching datapath
    srcversion:     90A0BE65A0CF67E0BEA2D57
    depends:
    vermagic:       3.5.0-40-generic SMP mod_unload modversions
