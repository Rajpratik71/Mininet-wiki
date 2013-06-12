# Expected results:
 Example code, documentation, and tutorials for automatically discovering and recreating a hardware topology in Mininet.

## Plan
To have a component in each controller(POX, Floodlight) that generates a mininet python file for the physical topology.


## Procedure
1. Create a module in controller which collects the following information:
> Host: IP + MAC
> Switch: MAC
> Link: each end's MAC + port no(if possible)

2. Using the above information and mininet API create mininet python file for physical topology.
