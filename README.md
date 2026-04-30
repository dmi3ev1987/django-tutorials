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
- Each field is represented by an instance of a `Field` class.
- The name of each `Field` instance is the field’s name and your database will use it as the *column* name.
- You can use an optional first positional argument to a `Field` to designate a human-readable name. See example for **Question.pub_date**.
- Some `Field` classes have required arguments. **CharField**, for example, requires that you give it a *max_length*.
- We’ve set the *default* value of *votes* to 0.
- Using **ForeignKey**. That tells Django each Choice is related to a single Question.

## [Activating models](https://docs.djangoproject.com/en/6.0/intro/tutorial02/#activating-models)

