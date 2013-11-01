***
## Introduction

In this demo, we are going to show you how Eve can eavesdrop the traffic between Alice and Bob by overflowing the mac address table in the switches. This attack would work even if Eve is not connected to the same switch used by Alice and Bob. The topology we use in the demo is shown in the following figure:
![Topology for MacOverFlowAttack](http://yuba.stanford.edu/~huangty/cs144/images/MacOverflowAttack.png)


## Get Started
### Environment Setup
Please refer to [[Environment Setup]] for setting up the Mininet environment.

### Check Out Starter Code
```no-highlight
~$ cd ~
~$ git clone https://huangty@bitbucket.org/huangty/cs144_security.git
~/cs144_security$ cd cs144_security/
```

### Link POX into the Directory
Please refer to [[Environment Setup]] for downloading the POX network controller.
Assuming you have the "pox" directory under the home directory. 
```no-highlight
~/cs144_security$ ln -s ~/pox/
```

### Configure the Environment
```no-highlight
~/cs144_security$ bash ./config.sh
```

## Mac Address Table Overflow Attack -- Demonstration
### Start POX Network Controller 
In one terminal:
```no-highlight
~/cs144_security$ ./run_pox.sh
```
This will start the POX network controller, which will emulate the behavior of a L2 learning switch. 

### Start Mininet Emulation 
In another terminal (if you are using a remote machine, make sure the X-forwarding is enabled.):
```no-highlight
~/cs144_security$ ./run.sh
```
This will start the Mininet network emulator and there will be terminals pops up for each of the nodes in the network. 

#### In Eve's Terminal:
