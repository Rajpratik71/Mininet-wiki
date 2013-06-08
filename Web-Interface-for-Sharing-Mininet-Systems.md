For this project, so far we have identified two main objectives:
- Something similar to PyPI or CPAN, but for Mininet
- Complete system/VM/experiment archive

BL Comments:

<i>My thinking is that we need to come up with something which is useful and also which is doable!!
I think we should break it down into things which can be done in 1 or 2-week increments if possible,
with something which can be demonstrated (at least in some basic way) after each period.</i>

<i>I do think the design is also important, however, as is the functionality. So to start with, I think it
would be a good idea to try to come up with a basic set of things that we would like to be able to
do with the system, considering what you could actually build without too much difficulty.</i>


### PyPI or CPAN for Mininet
For this objective, we firstly need a way to create a package for Mininet module/system first.
From what I know so far (hint: not very much), I think plain old python eggs are quite suitable for packaging a Mininet module/system. I need to take a closer look at python eggs to confirm this.

The next challenge is to actually create the website to host the packages.
Since I am strictly a leecher from PyPI, I will need to take a look at PyPI or CPAN or possibly other package repository websites to see what are the possible problems in building a website like these.


Update #1:

Looks like typical python source package format is good enough to store the Mininet modules/systems and their dependencies. If we use this format, then we will be able to use all existing python tools (e.g. pip) to automatically resolve, download and install dependencies and also to create and upload packages.  

We can either reuse existing PyPI or create a clone of PyPI exclusively to store Mininet modules/systems. If we create a clone, then we can simply redirect the package installer (e.g. pip) to download from the clone website instead of PyPI.  

Several available open source clone of PyPI are already available. These clones will be useful as a starting point so I won't have to reimplement to PyPI XML-RPC API (used by pip and easy_install internally). The one which I prefer so far is this one: https://pypi.python.org/pypi/djangopypi2. 

Update #2:

I have evaluated djangopypi2 and concluded that while the features we most likely want are not all supported, with some effort, they should all be implementable.

The features currently supported are:
- Registering/uploading package (or new version of package) through setup.py
- "pip install SomePackage" is working
- Support for package permission (e.g. who can upload new version of a package)

The features we want but not currently supported are:
- User interface for user registration (currently can only add user manually through admin panel)
- User interface for package maintainer to manage package permission (e.g. who can upload new version of a package)
- Search for packages through the website

Features not working, but not really needed:
- "pip search" does not work, but searching through the website should be enough

Other things:
- Wrapper script to upload/download modules. If we use djangopypi2 as our package server, then the command needed to upload/download modules are quite long e.g.:
    - Upload: python setup.py register -r local sdist upload -r local
    - Download: pip install --index-url http://localhost:8000/simple/ --extra-index-url https://pypi.python.org/simple/ SomePackage

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