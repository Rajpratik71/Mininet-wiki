## PyPI for Mininet / Mininet Module Repository
The purpose of this project is to enable users to share their modules and have other users to be able to easily install it.

### Packaging Format
Before any modules can be shared, a packaging format has to be defined for Mininet modules first. We have decided that the Mininet packaging format is just a plain old Python packaging format based on `setuptools` and `distutils`.

For a quick peek at how to convert existing modules to this packaging system, take a look at my commits:
- `pox`: https://github.com/heryandi/pox/commits/for_riplpox
- `ripl`: https://github.com/heryandi/ripl/commits/master
- `riplpox`: https://github.com/heryandi/riplpox/commits/master


### Components
- A website, [Mininet Repo](https://github.com/heryandi/djangopypi2), documentation available [here](https://github.com/heryandi/gsoc2013-onl-mininet/wiki/Mininet-Repo-Docs).
- Command-line tool, [mnp](https://github.com/heryandi/mnp), documentation available [here](https://github.com/heryandi/gsoc2013-onl-mininet/wiki/mnp:-Mininet-Packaging-Tools-Docs).

---

## Full System Experiment Archive
The purpose of this project is to create a website for researchers to share with others VM image that is ready to run their experiment. 

Documentation available [here](https://github.com/heryandi/gsoc2013-onl-mininet/wiki/Experiment-Repo-Docs).