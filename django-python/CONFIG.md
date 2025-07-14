# This Docs just talks about the proper way to setup up your config ("django-admin startproject config .") or your project in other words

First we will like to keep our production and development separate, also creating a base.py to default to.

create settings dir
.config/settings base.py dev.py prod.py

*** Note: Always enser your folders are modules by including __init__.py file in the dir

Next we have to point manage.py ./config/asgi.py and ./config/wsgi.py to our default settings modules,

We will be making use of .env instead of harcoding this everytime 
in .env

--- For development 
DJANGO_SETTINGS_MODULE=config.settings.dev
---
--- For Production
DJANGO_SETTINGS_MODULE=config.settings.prod
---

apps/auth user ...

in installed apps use
'apps.account.apps.AccountConfig',

with this you can easily use the name account in your settins

set name is app.py in each app
