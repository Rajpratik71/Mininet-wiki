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

For this part of project, my plan is to use the existing Python packaging system (based on `distribute`) for Mininet and build a PyPI clone just for Mininet modules.

The benefits of doing this are:
- Module dependencies can already be defined in the packaging system
- Ability to use existing Python tools (e.g.: `pip`, `setup.py`) to download and upload modules
- Tools to automatically resolve and download dependencies already exist (e.g.: `pip`, `easy_install`)

I intend to use `pip` and `setup.py` to download and upload packages respectively.

Since the tools already exist, then the only thing remaining is to build the PyPI clone itself. The PyPI clone should support:  
1. Package installation using `pip install`  
2. Package uploading using `python setup.py register sdist upload`  
3. Define package permission (e.g.: who can upload new version of package, who can delete package)  
4. UI to search for packages  
5. UI for user registration  
6. UI to manage the uploaded packages (e.g.: add/remove maintainer)  

Out of those 6, 1 & 2 are the most troublesome to implement because the website has to adhere to some convention in order for these to work.

I have already evaluated various existing open source PyPI clone and find that `djangopypi2` is quite functional and maintained relatively well. If I am allowed to use djangopypi2 as a base, then 1 & 2 are already done. However, I am still waiting for some reply regarding to the licensing issue.

Other things worth mentioning:
- Wrapper script to upload/download modules should be developed. If I use djangopypi2 as our package server, then the commands needed to upload/download modules are quite long e.g.:
  - Upload: `python setup.py register -r local sdist upload -r`  
  - Download: `pip install --index-url http://localhost:8000/simple/ --extra-index-url https://pypi.python.org/simple/ SomePackage`  
- If I use djangopypi2 then I will need to modify website texts accordingly (e.g.: change the website header to "Mininet Repository" or something).


BL Comments:

<i>I think it would be really useful to explain what the whole use case of this is. Consider, for example, that we might want to make a library of Mininet topologies that can be easily used. Can you explain how someone would find a topology, download it, and then use it from both the Mininet command line and from the script?

Also if possible I would like you to come up with an idea of the next things you'll be working on, preferably something that's small enough to complete and demonstrate in some form in the next week or two, and also write it down on this page so that I can take a look at it and give you some feedback and advice.
</i>

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