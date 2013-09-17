### Mininet 2.1.0

For details on the content and new features of 2.1.0, please consult the [README.md] (https://github.com/mininet/mininet/blob/2.1.0rc1/README.md) file.

### Potential Gotchas

* We've added a symlink to the top-level examples directory from the mininet code directory to make examples importable. If you are using VirtualBox and clone the repository to a shared folder, the symlink will be broken. This is because VirtualBox does not allow guests to create symlinks in shared folders by default for security reasons. To enable symlink creation, you should do the following:
    - Shutdown your VM and quit the VirtualBox Manager
    - Run the following command on your host OS: 
        `VBoxManage setextradata VM_NAME`
        `VBoxInternal2/SharedFoldersEnableSymlinksCreate/SHARE_NAME 1`
    - Restart your VM

### Errata

* Currently `--switch user` cannot be used with `--mac`; there appears to be a bug where it does not correctly match and forward packets when the MAC address is 00:00:00:00:00:01

* Currently `--switch ivs` cannot be used with more than a few switches; a bug has been submitted to the IVS developers

* Currently `--host rt` may not work correctly on multiprocessors (use `--host cfs` instead)