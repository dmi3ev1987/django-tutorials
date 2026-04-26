# [Part 1: Requests and responses](https://docs.djangoproject.com/en/6.0/intro/tutorial01/)

## Start new project

```bash
django-admin startproject mysite djangotutorial
```

This will create a directory `djangotutorial` with a project called `mysite` inside. 

```bash
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