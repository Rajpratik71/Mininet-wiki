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
- Preparing for deployment using `setup/config.py`.
  - Register new GitHub application here: https://github.com/settings/applications/new.  
    For callback url, make sure it is set to `<homepage_url>/accounts/github/login/callback/`.
  - Somehow get the `djangopypi2/setup/` folder out of the `gsoc` branch of https://github.com/heryandi/djangopypi2.
  - Set the application id and secret in `setup/conf/github_oauth.py`.
  - Put the HTTPS cert and key in `setup/keys/` folder.
  - Create a database.
  - Edit `setup/config.py` as needed. Most values are set appropriately but some changes may be needed, these values are the most likely to change:
     - `gunicorn_user` and `gunicorn_group`. Username and group name to execute gunicorn process.
     - `supervisor_user`. Username to execute supervisor.
     - `nginx_server_name`, `nginx_cert`, `nginx_cert_key`. Server name, https certificate and key filename.
     - `djpp_db_*`. The database settings to refer to previously created database.

- Deployment steps as done by `setup/config.py`
  - Install requirements available through apt-get
     - `apt-get install python-virtualenv nginx postgresql libpq-dev python-dev`
  - Create virtual environment for djpp
     - `virtualenv venv`
  - Clone https://github.com/heryandi/djangopypi2, checkout the `gsoc` branch.
     - `git clone https://github.com/heryandi/djangopypi2.git`
     - `git checkout -b gsoc`
  - Install the required packages (listed in setup/conf/requirements.txt).
     - `pip install -r requirements.txt`.
  - Configure supervisor and gunicorn and nginx.
     - See `setup/conf/gunicorn.sh` for gunicorn configuration template.
     - See `setup/conf/supervisord.conf` for supervisor configuration template.
     - See `setup/conf/nginx.conf` for nginx configuration template.
  - Initialize djpp stuff (static files, database content).
     - Also include the database entries to support GitHub authentication.
  - Run gunicorn, supervisor and nginx.
  