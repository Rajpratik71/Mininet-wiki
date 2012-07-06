<!-- %META:TOPICINFO{author="BobLantz" date="1305151271" format="1.1" reprev="1.2" version="1.2"}% -->
<!-- %META:TOPICPARENT{name="Mininet"}% -->
<!-- Use our custom page layout:
* Set VIEW_TEMPLATE = [MininetView](MininetView.html)
-->


### [Credits/History](Credits/History.html)

Mininet is under active development, and we hope it will become a highly usable and flexible virtual network platform for research, experimentation, debugging, learning, and many other applications. Most importantly, it's an easy and fun way to experiment with !OpenFlow!

Mininet is currently being developed by: Bob Lantz, who [created](MininetPresentations) the original system, built the virtual network infrastructure and CLI, and [demonstrated](http://www.openflowswitch.org/foswiki/pub/OpenFlow/MininetPresentations/mininet-presentation-2009.pdf) the initial implementation to the !OpenFlow Software Architecture and Implementation working group; Brandon Heller, who added many useful enhancements including regression tests, loglevels, Open vSwitch support, VM creation scripts, and Mininet creation from a separate topology description, as well as [documentation and tutorials](MininetDocumentation); and Nikhil Handigol and Vimal Jeyakumar, who are working on Mininet 2.0 features including CPU isolation and bandwidth limiting.

The developers encourage submission of patches that fix bugs or add useful features! If you would like to create or submit a patch (or join the Mininet development effort in general) please drop us a line on mininet-discuss.

Mininet's process-based virtualization approach uses Linux network namespaces, which were developed by Eric Biederman.
