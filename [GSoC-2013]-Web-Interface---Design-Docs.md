# PyPI for Mininet

## Components:
- Website: modified djangopypi2 ([https://github.com/heryandi/djangopypi2/tree/gsoc](https://github.com/heryandi/djangopypi2/tree/gsoc)).
- Command-line tool: mnp ([https://github.com/heryandi/mnp](https://github.com/heryandi/mnp)).
- Packaging format: existing python packaging system, based on `setuptools`.

### djangopypi2 (djpp):
- Usage: Downloading, uploading, searching package, browsing package information page, package permission management.
- User account: Local account and github account.
  - GitHub login supported by using [django-allauth](https://github.com/pennersr/django-allauth) library.

### mnp:
- Usage: See [https://github.com/heryandi/mnp](https://github.com/heryandi/mnp).
  - Implementation:
     - Download & upload: Calls other existing tools (e.g. `python setup.py` or `pip`).
     - The rest (`list`, `docs`, `search`, `info`) sends queries to djpp using xmlrpc.
         - Note: `docs`, `info` is not in [standard PyPI XMLRPC API](http://wiki.python.org/moin/PyPIXmlRpc).
- Login for uploading: Can use djpp account or github account.
  - For djpp account, username and password has to be specified in `.pypirc`.
     - Implementation: Simply calls `python setup.py` to do all the dirty job.
  - For github account, username has to be specified in `.pypirc`, password is optional.  
    Password is asked when `upload` command is executed if not specified in `.pypirc`.
     - Implementation: `python setup.py` is patched to read in the github username and password. In order for package uploaders to be able to use this feature, they will either have to manually specify the `cmdclass` included with `mnp` or by monkeypatching their `setup` function (can be done by simply `import mnp.patch`).
     - GitHub account authentication process:
         - GitHub username and password are read.
         - mnp sends request to GitHub to get list of tokens using the username and password.
         - Find the token used by djpp.
         - Hash (SHA1) the token and send the username and hashed token as password to djpp using xmlrpc.
         - djpp checks for authentication using its own account first, if it fails, then it will check if it is a GitHub account.
         - djpp hashes the token it remembers for the username and check if it matches the hash received from xmlrpc. If they match, login (and allow upload), otherwise, fail.

