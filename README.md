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

Examples of `Question` and `Choice` models you can find in `polls/models.py`.

- Each models has *variables* which represents a database *fields* in the model.
- Each field is represented by an instance of a `Field` class – e.g., **CharField** for character fields and **DateTimeField** for datetimes.
- The name of each `Field` instance is the field’s name and your database will use it as the *column* name.
- You can use an optional first positional argument to a `Field` to designate a human-readable name. See example for **Question.pub_date**.
- Some `Field` classes have required arguments. **CharField**, for example, requires that you give it a *max_length*.
- Optional argument for **Choice.votes** sets *default* value of *votes* to 0.
- **ForeignKey** in **Choice.question** tells Django each **Choice** is related to a single **Question**.

## [Activating models](https://docs.djangoproject.com/en/6.0/intro/tutorial02/#activating-models)

Tell our project that the polls app is installed:

```python
# mysite/settings.py
INSTALLED_APPS = [
    "polls.apps.PollsConfig",  # add a reference to PollsConfig in polls/apps.py
    "django.contrib.admin",
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    "django.contrib.messages",
    "django.contrib.staticfiles",
]
```

Now make migrations for one specific app **polls**. Name is set in *PollsConfig*.
This faster then make migrations for all apps by using just `python manage.py makemigrations`

```bash
python manage.py makemigrations polls
```

The `sqlmigrate` command takes *migration names* and returns their **SQL**:

```bash
python manage.py sqlmigrate polls 0001
```
> The sqlmigrate command doesn’t actually run the migration on your database - instead, it prints it to the screen so that you can see what SQL Django thinks is required. It’s useful for checking what Django is going to do or if you have database administrators who require SQL scripts for changes.

If you’re interested, you can also run `python manage.py check`. This checks for any problems in your project without making migrations or touching the database.

Now, run **migrate** again to create those model tables in your *database*:

```bash
python manage.py migrate
```

The `migrate` command takes all the migrations that haven’t been applied (Django tracks which ones are applied using a special table in your database called **django_migrations**).

Remember the three-step guide to making model changes:
- Change your models (in `models.py`).
- Run `python manage.py makemigrations` to create migrations for those changes.
- Run `python manage.py migrate` to apply those changes to the database.

Read the [django-admin documentation](https://docs.djangoproject.com/en/6.0/ref/django-admin/) for full information on what the `manage.py` utility can do.

## [Playing with the API](https://docs.djangoproject.com/en/6.0/intro/tutorial02/#playing-with-the-api)

