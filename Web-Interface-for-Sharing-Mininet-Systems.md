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

<i>BL: Having a separate project that uses the FreeBSD license is fine. It's just important for Mininet that user packages be open source and that everything that's part of Mininet proper be under Mininet's permissive BSD/MIT-style license. One interesting question is: do we have license requirements for submitted code? Probably we want to allow any Open Source license as PyPI does, and allow it to be specified on the package page. I think we may not want to require BSD-style licensing for all packages or Mininet-based systems that aren't part of Mininet proper, because some of them may include GPL code and we don't want to exclude them on licensing grounds. For full system images, we might consider permitting some closed-source components as long as they can be distributed freely, but the goal is to have something which people can examine and build upon.</i>

**Heryandi**  
Not sure what you mean here... Do you mean restrictions for the license of package submitted to the PyPI clone from GSoC? I don't think there is any restrictions on that, so any license should be fine. Note: Now that you brought up the licensing issue of full-system image, I updated the bottom part of this wiki as well.  
**End**

Other things worth mentioning:
- Wrapper script to upload/download modules should be developed. If I use djangopypi2 as our package server, then the commands needed to upload/download modules are quite long e.g.:
  - Upload: `python setup.py register -r local sdist upload -r local`  
  - Download: `pip install --index-url http://localhost:8000/simple/ --extra-index-url https://pypi.python.org/simple/ SomePackage`  

<i>BL: Agreed! We want to make it as easy as possible. I think we may want to include this script in the Mininet distribution itself, and also make it easily downloadable from the site. But I have a question: does pip not have anything like apt's sources.list?</i>

**Heryandi**  
There is a configuration file, although it works a little differently than /etc/apt/sources/list. The pip.conf file (described [here](http://www.pip-installer.org/en/latest/configuration.html)) can be used to set the default value of the command line arguments. So, if we specify the default value of `--extra-index-url` for `pip install <somepackage>` as `http://localhost:8000/simple/`, then `pip install` will first go to PyPI, and if it fails to find the package then it will visit `http://localhost:8000/simple/`. This definitely can work, but with a minor annoyance to have to fail in PyPI first before visiting `http://localhost:8000/simple/`.  

Well, either that or we can have a script to temporarily override the `PIP_CONFIG_FILE` environment variable to our own pip.conf and override the default value of `--index-url` rather than `--extra-index-url`, so `http://localhost:8000/simple/` will be visited first.  
**End**

- If I use djangopypi2 then I will need to modify website texts accordingly (e.g.: change the website header to "Mininet Repository" or something).

<i>BL: I think it would be really useful to explain what the whole use case of this is. Consider, for example, that we might want to make a library of Mininet topologies that can be easily used. Can you explain how someone would find a topology, download it, and then use it from both the Mininet command line and from the script? Also suppose someone has a new `Switch()` subclass - for example something that uses the Linux bridge or Click or creates a simple IP router - how would the creator of the script upload it to the system, and how would users find the new object, install it, figure out how to use it, and use it from either the command line or a Python script?</i>

<i>Also if possible I would like you to come up with an idea of the next things you'll be working on, preferably something that's small enough to complete and demonstrate in some form in the next week or two, and also write it down on this page so that I can take a look at it and give you some feedback and advice.
Follow-up: it looks like you've done that, great!
</i>

##### Use cases:
- Uploading a package:
  - User who wants to submit new package must register to the website.
  - User must create a file called `.pypirc` defining the URL of the PyPI and the login information. This is a one-time setup thing.
  - Once registered, for every package that the user wants to submit to the website, the user will have to write some package metadata in a `setup.py` file. The syntax of the package metadata will have to follow the syntax of typical Python package. Ideally, the user should write all of the followings: package name, package version, short description (1-2 sentences), long description (installation instruction, documentation etc) and the dependencies.
  - Once the setup.py file is written, the user will simply run `python setup.py register -r local sdist upload -r local` on the command line to automatically zip everything, register and upload the file to a PyPI called `local` (as defined in the `.pypirc`).

- Searching & downloading a package:
  - User do NOT need to register to download a package.
  - User must install pip on his own system. Available on Ubuntu with package name: `python-pip`.
  - To search for a package, user can go to the website and search according to some search criteria. User can also do a `pip search <term>` from a command line (waiting for a fix from djangopypi2 maintainer for this part).
  - From the search result, user can view the matching package page which will show the long description (as written by package uploader) and download the zipped package (without the dependencies) just like in real PyPI.
  - If user wants resolve all package dependencies automatically, user can install the package using pip just like this: `pip install --index-url http://localhost:8000/simple/ --extra-index-url https://pypi.python.org/simple/ SomePackage`.
  - `pip install` will automatically put all the executables in the package into one of the folders in $PATH (i.e. simply typing the executable name will work) and the python library in one of the folders in python paths (i.e. a simple `import something` in a python script will work). If `virtualenv` is used, then executables will go into `bin/` and library will go to `lib/pythonX.Y/site-packages/` under the virtual environment.
  - For uninstallation, `pip uninstall <package name>` will uninstall a package. For me, the best way is to use a `virtualenv` folder and simply delete the folder once I don't need it. I am not sure if Mininet users will like it though.

<i>BL: It's difficult for me to get my head around what we're actually creating here. Are we assuming that people are going to package their Mininet extensions as Python modules? Is that the right mechanism? Maybe it is, although it seems as if it might be overkill for something as simple as a topology, and perhaps inadequate for something like a multipath topology which requires a multipath controller.</i>

#### Plan for the this week (17-21 June):
- I am currently working on the user registration page (item no. 5 above) in djangopypi2. It currently only supports adding new user through the admin panel, which is not supposed to be accessible to anyone but the admin. This shouldn't take more than 2 days. [DONE]
- I will setup a free Amazon EC2 instance as well. I will set VNC and the djangopypi2 running there so you can track my progress by using VNC to the EC2 instance and running the browser. I have no idea how long this will take. [DONE]
- Remaining time will be spent to develop either of item no. 3, 4, 6. I will most likely leave item no. 4 for later though.

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