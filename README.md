# [Part 1: Requests and responses](https://docs.djangoproject.com/en/6.0/intro/tutorial01/)

## Start new project

```bash
django-admin startproject mysite djangotutorial
```

This will create a directory `djangotutorial` with a project called `mysite` inside. 

```
djangotutorial/
    manage.py
    mysite/
        __init__.py
        settings.py
        urls.py
        asgi.py
        wsgi.py
```

## Run server

You can change directory into `djangotutorial` and use default runserver command:

```bash
python manage.py runserver
```

You can change default `ip` and `port`.
Also you can run server from root directory:

```bash
python djangotutorial/manage.py runserver 127.0.0.1:8000
```

## Start new app

> An app is a web application that does something – e.g., a blog system, a database of public records or a small poll app. A project is a collection of configuration and apps for a particular website. A project can contain multiple apps. An app can be in multiple projects.

To create your app, make sure you’re in the same directory as `manage.py` and type this command:

```bash
python manage.py startapp polls
```

```
polls/
    __init__.py
    admin.py
    apps.py
    migrations/
        __init__.py
    models.py
    tests.py
    views.py
```

## First view and URL

Basic examples of `view` and `URL` you can find in `polls/views.py` and `polls/urls.py` respectively.