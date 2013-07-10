There are two main objectives:
- Something similar to PyPI or CPAN, but for Mininet
- Complete system/VM/experiment archive

### PyPI or CPAN for Mininet

For this part of project, we are going to use existing Python packaging system (based on `distribute`) to package Mininet modules and build a PyPI clone just to share Mininet modules.

The benefits of doing this are:
- Module dependencies can already be defined in the packaging system
- Ability to use existing Python tools (e.g.: `pip`, `setup.py`) to download and upload modules
- Tools to automatically resolve and download dependencies already exist (e.g.: `pip`, `easy_install`)

Since the tools already exist, then the only thing remaining is to build the PyPI clone itself. The PyPI clone should support:  
1. Package installation using `pip install`  
2. Package uploading using `python setup.py register sdist upload`  
3. UI to manage the uploaded packages (e.g.: add/remove maintainer, delete packages etc)  
4. UI to search for packages  
5. UI for user registration  

Other things worth mentioning:
- Wrapper script to upload/download modules should be developed. If I use djangopypi2 as our package server, then the commands needed to upload/download modules are quite long e.g.:
  - Upload: `python setup.py register -r local sdist upload -r local`  
  - Download: `pip install -i http://localhost:8000/simple/ --extra-index-url https://pypi.python.org/simple/ SomePackage`  

<i>BL: Agreed! We want to make it as easy as possible. I think we may want to include this script in the Mininet distribution itself, and also make it easily downloadable from the site. But I have a question: does pip not have anything like apt's sources.list?</i>

**Heryandi**  
There is a configuration file, although it works a little differently than /etc/apt/sources.list. The pip.conf file (described [here](http://www.pip-installer.org/en/latest/configuration.html)) can be used to set the default value of the command line arguments.  
**End**

- If I use djangopypi2 then I will need to modify website texts accordingly (e.g.: change the website header to "Mininet Repository" or something).

##### Use cases [moved [here](https://github.com/heryandi/gsoc2013-onl-mininet/wiki/Use-Cases)]

#### First iteration (17 June - 5 July):
- All missing major features are added to djangopypi2:
  - User registration page
  - Package permission page
  - Package search page
- Deployed the djangopypi2 on a free micro Amazon EC2 instance running on nginx + gunicorn. VNC can be used for remote access if needed. HTTPS is used for most parts of website except those under /simple/. The certificate used for HTTPS is currently self-signed though.
- Tried out the packaging system with more complicated dependencies. The Mininet modules used are: `mininet`, `pox`, `ripl`, `riplpox`.

----
#### BL: First Iteration Feedback

Great - it looks like this is a feasible proof of concept and the basic system is up and running to some extent!!

I'm interested in a bunch of things which might help to make this a real system that we could use:

0. Blocking issue: certificate download [RESOLVED]

1. Replicability/movability to a different cloud

    We now have one test server running in Amazon. If I want to replicate this server elsewhere (e.g. suppose we make a private cloud for on.lab using openstack), how do I do it? Is the process automatic? Can I still use Amazon for storage and as a CDN? If so, how?

**Heryandi**  
A script to set the server up in one step can definitely be written, though it will probably not be fully automatic.  
As for using Amazon only for storage/CDN, I am quite sure that it is possible with the API.  
For example, the python API is available [here](http://boto.readthedocs.org/en/latest/s3_tut.html).  
**Heryandi End**  

2. Help/documentation

    When people go to the site, where do they find clear instructions about how to find, download and use packages,  as well as how to create new packages and upload them? Is there a walkthrough showing how one might do this?

    When people go to a project page, how do they find out about the different options for downloading and/or installing a package?

    What is the learning curve? How do we make it as quick and easy as possible for people to find, install, and run/use a module?

    Many modules have documentation. How is that integrated into the system? How/where do I go to find documentation on a particular module? If I'm installing from the command line, how do I get the documentation?

**Heryandi**  
First 3 questions: No walkthrough yet, so I will need to write all these first. Once done, I can simply add a tab to the navigation bar for a link to the walkthrough. The link to the walkthrough page should probably be given in welcome email as well.

Module documentation: There is a package page which can show the documentation as written by uploader. If this is not enough, then a link to external website can also be added.

Command line documentation issues: a small script which queries the site by using the XML-RPC API can be written. If djangopypi2 currently doesn't support it, I don't think it will take long to implement it.  
**Heryandi End**  

3. Scripts

    Are there scripts to make these processes as easy as possible?  
**Heryandi**  
Nope, if there is, I will have to write it hahaha...
I intend to write scripts to shorten the upload/download package command though.  
**Heryandi End**  

4. How do you think people should find out about the system?  
**Heryandi**  
Never thought about this at all, maybe mention it in OpenFlow and Mininet tutorial?  
The people in Open Networking Lab should be "encouraged" to use it as well so at least someone starts using it...  
**Heryandi End**  

5. How are the data backed up? If everything stored on Amazon goes away, how do we recreate it?  
**Heryandi**  
Currently, there is no backup scheme at all...  
If we intend to use S3 though, there should be no issue on reliability (Note: I have never used S3, I may be wrong).  
We may want to do our own periodical backup (e.g.: every 1 month) just in case S3 screws up.  
**Heryandi End**  

6. Bug fixes and security updates

    How are bug fixes and security updates handled? What happens when there are critical security bugs in Linux/Ubuntu/Django/etc.?  
**Heryandi**  
If the bugs are in Linux/Ubuntu, it shouldn't break the code if Linux/Ubuntu is upgraded.  
If it affects Python/Django though, there may need to be some changes to the code if we upgrade. The best bet is to write unit tests with decent coverage so at least the breaking changes can be identified (and fixed manually >_<) quickly. Note: Currently there is almost no unit tests in djangopypi2!  
**Heryandi End**  

----

### Complete system/VM/experiment archive

This objective is to be able to quickly replicate experiments.

The idea is to create a place for users to submit either of these:
- Complete VM image that is ready to run the experiment.  
  Pros: Pretty much guaranteed that the experiment will be able to work.  
  Cons: Users will need to upload/download huge files. Storage space consumed will also be large.
- Executable script to setup all the configurations needed (e.g.: download and install some packages from apt-get or PyPI) to run the experiment.  
  Pros: Users will not need to upload/download huge files. Storage space consumed will also be smaller.  
  Cons: May not always be successful in replicating experiments because of various problems (e.g.: some packages or some version of the packages may not be available anymore, different mininet version, different version of Linux).

The item uploaded will be submitted together with a post describing the content and the instruction to replicate the experiment (like the blog posts on reproducingnetworkresearch.wordpress.com).

Note that this "place" for submitting the experiment can possibly be the same website for the previous objective above or reproducingnetworkresearch.wordpress.com or a totally new website since it may be used for more than just Mininet experiments.

**Update**  
Some experiment codes only work under certain flavor/version of Linux, especially kernel code. In this case, complete VM image is the only way to reproduce the experiment easily.  
Sometimes experiments may also make use of closed source component. If this closed source component is freely distributable, then the only way to reproduce the experiment easily is again using full VM image.  
Before we rule out the second choice though, is there a network experiment which requires the experiment to be run on real machines (i.e. not VM)?

#### BL Comments

Some questions I think we need to think about or investigate:

Storage Issues:

* What can we use for scalable storage/CDN?
* Can/should we use S3? How much does it cost? How are credentials handled? 
* What are other issues relating to VM image storage, which might require a LOT of storage?  
**Heryandi**  
Storage/CDN/S3: Need to investigate about this first.  
Others: Uploading the image itself is a pain because of the size... Probably something which supports resuming will be needed.  
**Heryandi End**  


Deployment issues

* What are the target deployment platforms?
* Can the same image be used on, say, EC2 and VirtualBox?  
**Heryandi**  
Deployment platforms: Not sure what you meant by deployment platforms. VirtualBox, VMware and KVM?  
EC2 & VBox: Need to investigate first.  
**Heryandi End**  

Web front-end issues:

* Can we have a single sign-on for both packages and VM images?  
**Heryandi**  
If both sites can access the same user database, I am sure that this can be done.  
If not, there should be a way though I am not sure how.  
**Heryandi End**  

User Issues:

* What are the specific foolproof steps that users will take?
* Is there a template for documentation of each system, sort of like what we had on reproducingnetworkresearch.org?
* Is there a means for users to add additional information or documentation about a system?
* How will users find out about this system?
* How will it be documented?
