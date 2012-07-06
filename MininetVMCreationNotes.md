<!-- %META:TOPICINFO{author="BobLantz" date="1337840332" format="1.1" reprev="1.37" version="1.37"}% -->
<!-- %META:TOPICPARENT{name="Mininet"}% -->
<!-- Use our custom page layout:
* Set VIEW_TEMPLATE = [MininetView](MininetView.html)
-->


Creating a Mininet VM from Scratch
===================================

These instructions cover the process of building a Mininet-capable VM from scratch, using Ubuntu 11.10. For setup instructions, see [MininetVMSetupNotes](MininetVMSetupNotes.html).

_Note: If you need to build on a non-Ubuntu distro, you are currently on your own. The included shell scripts may work on your platform, but significant changes are likely (e.g. yum vs. apt-get and different package names for Fedora.)_

<strong>First, create a new Ubuntu 11.10 VM </strong>(using VMware, !VirtualBox, etc..) We use user: openflow and password: openflow in our default VM, but any user/password combo should work as long as it has admin/sudo privileges.

**Next, log in to the new VM and do the following**:
<verbatim>
wget https://raw.github.com/mininet/mininet/util/vm/install-mininet-vm.sh
time install-mininet-vm.sh
</verbatim> After this completes (about 15-20 minutes), Mininet should work: <verbatim>
$ sudo mn --pingall
</verbatim>

**That's it!**

For historical reference, we have also preserved the [OldMininetVMCreationNotes](OldMininetVMCreationNotes.html).

-- [Main/BrandonHeller](../Main/BrandonHeller.html) - 09 Feb 2010
