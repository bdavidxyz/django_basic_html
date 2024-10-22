# Django skeleton with basic HTML page

Django project with Postgre.

## Prerequisites

Ensure that `py` command points to python3 with `py -V`

Check that pg is running on your machine with `pg_ctl status` (Windows), or `pgrep -l postgres` (Mac), or `sudo systemctl status postgresql` (Linux)

## Create a new folder

```shell
mkdir webplus && cd webplus
```

## Add .gitignore and start git

Add the following .gitignore

```shell
.venv
*.sqlite3
__pycache__
```

## Prepare PostgreSQL

```shell
sudo -u postgres psql
```

```sql
CREATE DATABASE webplus;

CREATE USER webplususer WITH PASSWORD 'password';

ALTER ROLE webplususer SET client_encoding TO 'utf8';
ALTER ROLE webplususer SET default_transaction_isolation TO 'read committed';
ALTER ROLE webplususer SET timezone TO 'UTC';

GRANT ALL PRIVILEGES ON DATABASE webplus TO webplususer;
\q

```

## Create virtual env and activate it

```shell
py -m venv .venv
source .venv/bin/activate
```

## Install django and pg driver

```shell
py -m pip install django psycopg2-binary
```

## Build django skeleton

Below, "core" means the core of our app, "." means build skeleton inside the current folder :

```shell
django-admin startproject core .
```

## Change settings.py

Inside settings.py, add localhost to the list of allowed hosts, like this :

```py
ALLOWED_HOSTS = ['localhost', '127.0.0.1']
```

And database settings like this :

```py
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql_psycopg2',
        'NAME': 'webplus',
        'USER': 'webplususer',
        'PASSWORD': 'password',
        'HOST': 'localhost',
        'PORT': '',
    }
}
```

## Initial migrations

Django comes with default tables (to manage users and authorizations notably), so let's inject them into our new db :

```shell
py manage.py makemigrations
py manage.py migrate
```
