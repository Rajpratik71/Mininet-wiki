*BL Comments: I think we need more detail here about what the actual functionality would be - what sort of tasks this would enable!!*

## Raspberry Pi Mininet image (network in a box)
The project will be extremely useful for educational institutions, as they would now be able to use extremely cheap test beds for networking and allied courses.

*BL: I think the key aspect of an rPi Mininet image is creating a script which can generate that image automatically!! Here are some questions: Does Mininet run in the stock Ubuntu image for the Pi? If so, then we're already done. If not, what do we need to do to get it to run? We might need a way of automatically creating a cross-development environment to compile an ARM kernel for the Pi that has CONFIG_NETNS enabled. Then we would need a way of creating a new image which has the new kernel and Mininet in it, and hopefully have some automatic way of testing the image to make sure that it actually works, for example booting it in qemu for ARM.*

## Mininet Automatic Deployment to Amazon EC2  
The project is an interesting and a very useful addition to Mininet that will enhance it's usability. It will be quite tricky to make the software simulation of Mininet to work across multiple nodes interacting with the physical resources.