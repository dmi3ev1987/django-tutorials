# [Part 2: Models and the admin site](https://docs.djangoproject.com/en/6.0/intro/tutorial02/)

## [Database setup](https://docs.djangoproject.com/en/6.0/intro/tutorial02/#database-setup)

```bash
python manage.py migrate
```

> The `migrate` command looks at the `INSTALLED_APPS` setting and creates any necessary database tables according to the database settings in your `mysite/settings.py` file (SQLite by default).

For SQLite you can check tables created:

```bash
# verify sqlite3 is installed
sqlite3 --version

# enter sqlite3 shell > path to db.sqlite3
sqlite3 djangotutorial/db.sqlite3 

# display the tables Django created
.tables

# use .quit or .exit
.exit
```

To use another database, see [details to customize and get your database running](https://docs.djangoproject.com/en/6.0/topics/install/#database-installation).

## [Creating models](https://docs.djangoproject.com/en/6.0/intro/tutorial02/#creating-models)
