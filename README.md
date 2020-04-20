# Instructions

### Install dependencies
- $ virtualenv -p python3 ~/infiniteshop # create virtual environment
- $ source ~/inifiniteshop/bin/activate # activate virtual environment
- (infiniteshop) $ # when virtual environment is activated
- (infiniteshop) $  pip install django-oscar
- (infiniteshop) $  pip install sorl-thumbnail
- (infiniteshop) $  pip install pycountry
- (infiniteshop) $  django-admin.py startproject infiniteshop

### Modify Django settings
Import Oscar defaults
```python
# settings.py

import os

# after import os
from oscar.defaults import *
```

Then add Oscar context processors

```python
# inifiniteshop/settings.py

TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
                # oscars context processors
                'oscar.apps.search.context_processors.search_form',
                'oscar.apps.checkout.context_processors.checkout',
                'oscar.apps.customer.notifications.context_processors.notifications',
                'oscar.core.context_processors.metadata',


            ],
        },
    },
]
```

Modify installed apps to include Oscar applications and dependencies

```python
# inifiniteshop/settings.py

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',

    'django.contrib.sites',
    'django.contrib.flatpages',

    'oscar',
    'oscar.apps.analytics',
    'oscar.apps.checkout',
    'oscar.apps.address',
    'oscar.apps.shipping',
    'oscar.apps.catalogue',
    'oscar.apps.catalogue.reviews',
    'oscar.apps.partner',
    'oscar.apps.basket',
    'oscar.apps.payment',
    'oscar.apps.offer',
    'oscar.apps.order',
    'oscar.apps.customer',
    'oscar.apps.search',
    'oscar.apps.voucher',
    'oscar.apps.wishlists',
    'oscar.apps.dashboard',
    'oscar.apps.dashboard.reports',
    'oscar.apps.dashboard.users',
    'oscar.apps.dashboard.orders',
    'oscar.apps.dashboard.catalogue',
    'oscar.apps.dashboard.offers',
    'oscar.apps.dashboard.partners',
    'oscar.apps.dashboard.pages',
    'oscar.apps.dashboard.ranges',
    'oscar.apps.dashboard.reviews',
    'oscar.apps.dashboard.vouchers',
    'oscar.apps.dashboard.communications',
    'oscar.apps.dashboard.shipping',

    # 3rd-party apps that oscar depends on
    'widget_tweaks',
    'haystack',
    'treebeard',
    'sorl.thumbnail',
    'django_tables2',
]

SITE_ID = 1
```

Add Oscar middlewares and authentication backend

```python
# inifiniteshop/settings.py

MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',

    # oscar middleware
    'oscar.apps.basket.middleware.BasketMiddleware',
    'django.contrib.flatpages.middleware.FlatpageFallbackMiddleware',
]

AUTHENTICATION_BACKENDS = (
    'oscar.apps.customer.auth_backends.EmailBackend',
    'django.contrib.auth.backends.ModelBackend',
)

```

Add Oscar urls and i18n for multiple language


```python
# inifiniteshop/urls.py
from django.apps import apps
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('i18n/', include('django.conf.urls.i18n')), # urls for multi language
    path('', include(apps.get_app_config('oscar').urls[0])), # oscar urls
]

```

Add Haystack connection


```python
# inifiniteshop/settings.py

...

HAYSTACK_CONNECTIONS = {
    'default': {
        'ENGINE': 'haystack.backends.simple_backend.SimpleEngine',
    },
}

```

> After setting up all of the mentioned above do the following so we can run the server.

Create database with the current migrations from oscar and django. Create superuser
- (infiniteshop) $ python manage.py migrate
- (infiniteshop) $ python manage.py runserver
- (infiniteshop) $ python manage.py createsuperuser

> You should now have an empty, but running Oscar install that you can browse at http://localhost:8000.

> Oscar shipped with its own dashboard that you can access at http://localhost:8000/dashboard/.


Initial data for countries
- (infiniteshop) $ python manage.py oscar_populate_countries --no-shipping # omit --no-shipping if you want all countries to be use for shipping 
