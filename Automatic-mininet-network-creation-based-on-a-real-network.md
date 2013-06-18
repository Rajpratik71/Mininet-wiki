## Expected results:
 Example code, documentation, and tutorials for automatically discovering and recreating a hardware topology in Mininet.

## Plan
To have a component in each controller(POX, Floodlight) that generates a mininet python file for the physical topology.


## Procedure
* Create a module in controller which collects the following information:

> Host: IP + MAC

> Switch: MAC

> Link: each end's MAC + port no(if possible)

* Using the above information and mininet API, create mininet python file for physical topology.

BL Comments: This sounds good to me. I know I'm not mentoring you officially, but I think that you will want to make a wiki page that explains how to install and use your system, perhaps with instructions and a walkthrough as well as API documentation for people who want to integrate your module into their POX/Floodlight controller. I recommend making it as simple as possible for someone to make use of your mininet topology creation module -> for example it should be as easy as downloading and running a script and then getting a custom topology file which can be used directly from the mininet command line or from another script.