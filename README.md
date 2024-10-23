# Django skeleton with 2 basic HTML pages

Django project with Postgre, 2 html pages, and link between them.

## Prerequisites

Ensure that `py` command points to python3 with `py -V` (python 3 or above)

Check that pg 16+ is running on your machine with `pg_ctl status` (Windows), or `pgrep -l postgres` (Mac), or `sudo systemctl status postgresql` (Linux)

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

```shell
git init
git add . && git commit -m "first commit"
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

## (Optional) Create a superadmin locally

Create a super user, just enter the following command and enter some realistic values when asked :

```shell
py manage.py createsuperuser
```

## Create templates

Create templates/home.html

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <title>Home page</title>
  </head>
  <body>
    <h1>Home</h1>
    <p>I'm the home page. Go to <a href="/about">about</a> page.</p>
  </body>
</html>
```

Create templates/about.html

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <title>About page</title>
  </head>
  <body>
    <h1>About</h1>
    <p>It's about me. Back to <a href="/">home</a> page.</p>
  </body>
</html>
```

## Make the app aware of the templates folder

Inside core/settings.py, update the 'DIRS' prop like this :

```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': ['templates'], # <--- add 'templates' here
        # ... other props
    }
]
```

## Create views

Create `core/views.py`

```python
from django.shortcuts import render

def homepage(request):
    return render(request, 'home.html')

def aboutpage(request):
    return render(request, 'about.html')

```

## Map URLs to views

Inside `core/urls.py`

```python
from django.contrib import admin
from django.urls import path
from . import views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', views.homepage),
    path('about', views.aboutpage),
]
```

## Run server

```shell
py manage.py runserver
```

And navigate between home and about pages.
