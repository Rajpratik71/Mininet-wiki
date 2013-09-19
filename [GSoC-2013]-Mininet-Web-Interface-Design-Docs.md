# PyPI for Mininet

## Components:
- Website: modified djangopypi2 ([https://github.com/heryandi/djangopypi2/tree/gsoc](https://github.com/heryandi/djangopypi2/tree/gsoc)).
- Command-line tool: mnp ([https://github.com/heryandi/mnp](https://github.com/heryandi/mnp)).
- Packaging format: existing python packaging system, based on `setuptools`.

### djangopypi2 (djpp):
- Usage: Downloading, uploading, searching package, browsing package information page, package permission management.
- User account: Local account and github account.
  - GitHub login supported by using [django-allauth](https://github.com/pennersr/django-allauth) library.
     - Logging in with GitHub account will create a new local account with no password.  
       Note: No password means "cannot login with any password" not "can login without password".  
       User can set email and password if s/he wants later.