# PyPI for Mininet

## Components:
- Website: modified djangopypi2 (`https://github.com/heryandi/djangopypi2/tree/gsoc`)
- Command-line tool: mnp (`https://github.com/heryandi/mnp`)

### djangopypi2:
- Usage: Downloading, uploading, searching package and browsing package information page
- User Account: Local account and github account
  - GitHub account supported by using django-allauth library

### mnp:
- Usage: See `https://github.com/heryandi/mnp`
- Each command mainly calls other existing tools (e.g. `python setup.py` or `pip`)
- User login for uploading: Can use djangopypi2 local account or github account
  - For djangopypi2 account, username and password has to be specified in `.pypirc`.
  - For github account, username has to be specified in `.pypirc`, password is optional.


## Packaging format:
- Using existing python packaging system, based on `setuptools`