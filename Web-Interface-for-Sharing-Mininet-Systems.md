For this project, so far we have identified two main objectives:
- Something similar to PyPI or CPAN, but for Mininet
- Complete system/VM/experiment archive

### PyPI or CPAN for Mininet
For this objective, we firstly need a way to create a package for Mininet module/system first.
From what I know so far (hint: not very much), I think plain old python eggs are quite suitable for packaging a Mininet module/system. I need to take a closer look at python eggs to confirm this.

The next challenge is to actually create the website to host the packages.
Since I am strictly a leecher from PyPI, I will need to take a look at PyPI or CPAN or possibly other package repository websites to see what are the possible problems in building a website like these.

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