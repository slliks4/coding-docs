# Create env file in project dir
export PIPENV_VENV_IN_PROJECT=1
pipenv shell

# Install Django
pip install Django

# Start Django project
django-admin startproject config .
## Note: "config" is the recommended naming convention, feel free to stick with anything you like

# Start Django app
python manage.py startapp appname
## Note: "core" is the recommended naming convention for the main app

# Setup Environmental Variables 
## .env file
### Core Settings
DEBUG=False
SECRET_KEY=your-very-secret-key
ALLOWED_HOSTS=yourdomain.com,localhost,127.0.0.1
TIME_ZONE=

### CORS (if using CORS headers)
CORS_ALLOWED_ORIGINS=https://yourfrontend.com,https://admin.yourdomain.com
CSRF_TRUSTED_ORIGINS=https://yourfrontend.com

### Database
DB_NAME=yourdbname
DB_USER=yourdbuser
DB_PASSWORD=yourdbpassword
DB_HOST=localhost
DB_PORT=5432

### Email (for error reporting, password resets, etc.)
EMAIL_HOST=smtp.yourprovider.com
EMAIL_PORT=587
EMAIL_HOST_USER=you@yourdomain.com
EMAIL_HOST_PASSWORD=super-secret-password
EMAIL_USE_TLS=True

### Static & Media file configuration (if using remote storage)
AWS_ACCESS_KEY_ID=your-aws-access-key
AWS_SECRET_ACCESS_KEY=your-aws-secret
AWS_STORAGE_BUCKET_NAME=your-bucket-name

### Logging (Optional but useful)
LOG_LEVEL=INFO

## Load Environmental Variables in settings.py
import os

DEBUG = os.environ.get('DEBUG', 'False') == 'True'

SECRET_KEY = os.environ['SECRET_KEY']  # crash early if missing

ALLOWED_HOSTS = os.environ.get('ALLOWED_HOSTS', '').split(',')
Note: split turns it into a list.

TIME_ZONE = os.environ.get('TIME_ZONE', 'UTC')

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': os.environ['DB_NAME'],
        'USER': os.environ['DB_USER'],
        'PASSWORD': os.environ['DB_PASSWORD'],
        'HOST': os.environ.get('DB_HOST', 'localhost'),
        'PORT': os.environ.get('DB_PORT', '5432'),
    }
}

### Note: Django does not automatically load .env files. Unless you're using a library like python-dotenv, django-environ, or python-decouple, or you're exporting variables manually, the .env file won’t be read automatically by python manage.py runserver.

So here's a tiny wrapper script that manually loads .env into os.environ before running Django, with zero dependencies:

import os
from pathlib import Path

#### ✅ Load .env manually
env_file = BASE_DIR / '.env'

if env_file.exists():
    with open(env_file) as f:
        for line in f:
            if line.strip() and not line.startswith('#'):
                key, value = line.strip().split('=', 1)
                os.environ[key] = value  # ✅ overwrite every time

or use 
os.environ.setdefault(key, value)
Need to restart shell if changes are made to the .env file, while the other one overwrite every time no need to restart shell

Note: For production, Use bash, docker e.t.c to load .env files.


# Setup Templates
Depends on where your templates folder is located, in general we will like to set it in a way it sees all dir named templates to avoid any error
In settigs.py
Templates[
{
    'DIRS': [os.path.join(BASE_DIR, 'templates'),],  # global project templates 
        'APP_DIRS': True,  # app-level templates (e.g., core/templates/)
}
]

# Setup Static Files
DEBUG MODE:
STATICFILES_DIRS = [os.path.join(BASE_DIR, 'static')] # Global or shared
STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles') # For production (collectstatic)
PRODUCTION MODE:
Use Aws or other provider
STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'

    Then in your config.urls.py
urlpatterns += static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)

# Media Upload
    MEDIA_URL='media/'
    MEDIA_ROOT=os.path.join(BASE_DIR, 'media')

    Then in your config.urls.py
urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)

## NOTE: ALWAYS REMEMBER TO ADD YOUR APP AMONG INSTALLED APPS!




