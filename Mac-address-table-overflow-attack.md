***
## Introduction

In this demo, we are going to show you how Eve can eavesdrop the traffic between Alice and Bob by overflowing the mac address table in the switches. This attack would work even if Eve is not connected to the same switch used by Alice and Bob. The topology we use in the demo is shown in the following figure:
![Topology for MacOverFlowAttack](http://yuba.stanford.edu/~huangty/cs144/images/MacOverflowAttack.png)


## Get Started
### Environment Setup
Please refer to [[Environment Setup]] for setting up the environment.

### Check Out Starter Code
```no-highlight
cd ~
git clone https://huangty@bitbucket.org/huangty/cs144_lab3.git
cd cs144_lab3/
git checkout --track remotes/origin/standalone
```