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

```bash
python manage.py shell
```

When you are in the **shell** type commands after `>>>`:

```python
# Support for time zones is enabled in the default settings file, so
# Django expects a datetime with tzinfo for pub_date. Use timezone.now()
# instead of datetime.datetime.now() and it will do the right thing.
>>> from django.utils import timezone
>>> q = Question(question_text="What's new?", pub_date=timezone.now())

# Save the object into the database. You have to call save() explicitly.
>>> q.save()

# Now it has an ID.
>>> q.id
1

# Access model field values via Python attributes.
>>> q.question_text
"What's new?"
>>> q.pub_date
datetime.datetime(2012, 2, 26, 13, 0, 0, 775217, tzinfo=datetime.UTC)

# Change values by changing the attributes, then calling save().
>>> q.question_text = "What's up?"
>>> q.save()

# objects.all() displays all the questions in the database.
>>> Question.objects.all()
<QuerySet [<Question: Question object (1)>]>
```

To make a helpful representation of `Question` and `Choice` objects add a `__str__()` methods in the `polls/models.py` file.

Also we can add a custom method `was_published_recently` to `Question` model.

> Note the addition of `import datetime` and `from django.utils import timezone`, to reference Python’s standard `datetime` module and Django’s time-zone-related utilities in `django.utils.timezone`, respectively. If you aren’t familiar with time zone handling in Python, you can learn more in the [time zone support docs](https://docs.djangoproject.com/en/6.0/topics/i18n/timezones/).

If a three-chevron prompt (>>>) indicates you are still in the **shell**, you need to exit first using `exit()`. Run `python manage.py shell` again to reload the models.

```python
>>> Question.objects.all()
>>> Question.objects.filter(id=1)
>>> Question.objects.filter(question_text__startswith="What")

# Get the question that was published this year.
>>> from django.utils import timezone
>>> current_year = timezone.now().year
>>> Question.objects.get(pub_date__year=current_year)
<Question: What's up?>

# The following is identical to Question.objects.get(id=1).
>>> Question.objects.get(pk=1)
<Question: What's up?>

# Make sure our custom method worked.
>>> q = Question.objects.get(pk=1)
>>> q.was_published_recently()
True

# Give the Question a couple of Choices.
>>> q = Question.objects.get(pk=1)
>>> q.choice_set.create(choice_text="Not much", votes=0)
>>> q.choice_set.create(choice_text="The sky", votes=0)

>>> c = q.choice_set.create(choice_text="Just hacking again", votes=0)

# Choice objects have API access to their related Question objects.
>>> c.question
<Question: What's up?>

# And vice versa: Question objects get access to Choice objects.
>>> q.choice_set.all()
>>> q.choice_set.count()

# The API automatically follows relationships as far as you need.
# Use double underscores to separate relationships.
# This works as many levels deep as you want; there's no limit.
>>> Choice.objects.filter(question__pub_date__year=current_year)

# Let's delete one of the choices. Use delete() for that.
>>> c = q.choice_set.filter(choice_text__startswith="Just hacking")
>>> c.delete()
```

For more information on model relations, see [Accessing related objects](https://docs.djangoproject.com/en/6.0/ref/models/relations/). For more on how to use double underscores to perform field lookups via the API, see [Field lookups](https://docs.djangoproject.com/en/6.0/topics/db/queries/#field-lookups-intro). For full details on the database API, see our [Database API reference](https://docs.djangoproject.com/en/6.0/topics/db/queries/).

## [Introducing the Django Admin](https://docs.djangoproject.com/en/6.0/intro/tutorial02/#introducing-the-django-admin)

### [Creating an admin user](https://docs.djangoproject.com/en/6.0/intro/tutorial02/#creating-an-admin-user)

```bash
python manage.py createsuperuser
```

### [Start the development server](https://docs.djangoproject.com/en/6.0/intro/tutorial02/#start-the-development-server)

```bash
python manage.py runserver
```

Open a web browser and go to `/admin/` on your local domain – e.g., `http://127.0.0.1:8000/admin/`.
Since [translation](https://docs.djangoproject.com/en/6.0/topics/i18n/translation/) is turned on by default, if you set [LANGUAGE_CODE](https://docs.djangoproject.com/en/6.0/ref/settings/#std-setting-LANGUAGE_CODE), the login screen will be displayed in the given language.

### [Enter the admin site](https://docs.djangoproject.com/en/6.0/intro/tutorial02/#enter-the-admin-site)

Log in with the superuser account you created.

### [Make the poll app modifiable in the admin](https://docs.djangoproject.com/en/6.0/intro/tutorial02/#make-the-poll-app-modifiable-in-the-admin)

To see poll app in admin we need to edit `polls/admin.py`.

```python
from django.contrib import admin

from .models import Question

admin.site.register(Question)
```

### [Explore the free admin functionality](https://docs.djangoproject.com/en/6.0/intro/tutorial02/#explore-the-free-admin-functionality)