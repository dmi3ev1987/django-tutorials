# [Part 5: Testing](https://docs.djangoproject.com/en/6.0/intro/tutorial05/)

## [Introducing automated testing](https://docs.djangoproject.com/en/6.0/intro/tutorial05/#introducing-automated-testing)

### [What are automated tests?](https://docs.djangoproject.com/en/6.0/intro/tutorial05/#what-are-automated-tests)

In automated tests the testing work is done for you by the system. You create a set of tests once, and then as you make changes to your app, you can check that your code still works as you originally intended, without having to perform time consuming manual testing.

### [Why you need to create tests](https://docs.djangoproject.com/en/6.0/intro/tutorial05/#why-you-need-to-create-tests)

#### [Tests will save you time](https://docs.djangoproject.com/en/6.0/intro/tutorial05/#tests-will-save-you-time)

Automated tests could do checking for you in seconds. Tests will also assist in identifying the code that’s causing the unexpected behavior.

#### [Tests don’t just identify problems, they prevent them](https://docs.djangoproject.com/en/6.0/intro/tutorial05/#tests-don-t-just-identify-problems-they-prevent-them)

#### [Tests make your code more attractive](https://docs.djangoproject.com/en/6.0/intro/tutorial05/#tests-make-your-code-more-attractive)

> You might have created a brilliant piece of software, but you will find that many other developers will refuse to look at it because it lacks tests; without tests, they won’t trust it. Jacob Kaplan-Moss, one of Django’s original developers, says “Code without tests is broken by design.”

> That other developers want to see tests in your software before they take it seriously is yet another reason for you to start writing tests.

#### [Tests help teams work together](https://docs.djangoproject.com/en/6.0/intro/tutorial05/#tests-help-teams-work-together)

> If you want to make a living as a Django programmer, you must be good at writing tests!

## [Basic testing strategies](https://docs.djangoproject.com/en/6.0/intro/tutorial05/#basic-testing-strategies)

> Some programmers follow a discipline called “[test-driven development](https://en.wikipedia.org/wiki/Test-driven_development)”; they actually write their tests before they write their code. This might seem counterintuitive, but in fact it’s similar to what most people will often do anyway: they describe a problem, then create some code to solve it. Test-driven development formalizes the problem in a Python test case.

## [Writing our first test](https://docs.djangoproject.com/en/6.0/intro/tutorial05/#writing-our-first-test)

### [We identify a bug](http://docs.djangoproject.com/en/6.0/intro/tutorial05/#we-identify-a-bug)

> Fortunately, there’s a little bug in the polls application for us to fix right away: the Question.was_published_recently() method returns True if the Question was published within the last day (which is correct) but also if the Question’s pub_date field is in the future (which certainly isn’t).

### [Create a test to expose the bug](https://docs.djangoproject.com/en/6.0/intro/tutorial05/#create-a-test-to-expose-the-bug)

A conventional place for an application’s tests is in the application’s `tests.py` file; the testing system will automatically find tests in any file whose name begins with `test`.

Check out `polls/tests.py`

> Here we have created a django.test.TestCase subclass with a method that creates a Question instance with a pub_date in the future. We then check the output of was_published_recently() - which ought to be False.

### [Running tests](https://docs.djangoproject.com/en/6.0/intro/tutorial05/#running-tests)

In the terminal, we can run our test:

```bash
python manage.py test polls
```

What happened is this:

- `manage.py test polls` looked for tests in the `polls` application
- it found a subclass of the [django.test.TestCase](https://docs.djangoproject.com/en/6.0/topics/testing/tools/#django.test.TestCase) class
- it created a special database for the purpose of testing
- it looked for test methods - ones whose names begin with `test`
- in `test_was_published_recently_with_future_question` it created a `Question` instance whose `pub_date` field is 30 days in the future
- … and using the `assertIs()` method, it discovered that its `was_published_recently()` returns `True`, though we wanted it to return `False`

The test informs us which test failed and even the line on which the failure occurred.

### [Fixing the bug](https://docs.djangoproject.com/en/6.0/intro/tutorial05/#fixing-the-bug)

Amend the method in models.py, so that it will only return True if the date is also in the past:

```python
# polls/models.py
def was_published_recently(self):
    now = timezone.now()
    return now - datetime.timedelta(days=1) <= self.pub_date <= now
```

### [More comprehensive tests](https://docs.djangoproject.com/en/6.0/intro/tutorial05/#more-comprehensive-tests)

Add two more test methods to the same class, to test the behavior of the method more comprehensively:

```python
# polls/tests.py
def test_was_published_recently_with_old_question(self):
    """
    was_published_recently() returns False for questions whose pub_date
    is older than 1 day.
    """
    time = timezone.now() - datetime.timedelta(days=1, seconds=1)
    old_question = Question(pub_date=time)
    self.assertIs(old_question.was_published_recently(), False)


def test_was_published_recently_with_recent_question(self):
    """
    was_published_recently() returns True for questions whose pub_date
    is within the last day.
    """
    time = timezone.now() - datetime.timedelta(hours=23, minutes=59, seconds=59)
    recent_question = Question(pub_date=time)
    self.assertIs(recent_question.was_published_recently(), True)
```

And now we have three tests that confirm that `Question.was_published_recently()` returns sensible values for past, recent, and future questions.

## [Test a view](https://docs.djangoproject.com/en/6.0/intro/tutorial05/#test-a-view)

Setting a pub_date in the future should mean that the Question is published at that moment, but invisible until then.

### [A test for a view](https://docs.djangoproject.com/en/6.0/intro/tutorial05/#a-test-for-a-view)

> When we fixed the bug above, we wrote the test first and then the code to fix it. In fact that was an example of **test-driven development**, but `it doesn’t really matter in which order we do the work`.

Before we try to fix anything, let’s have a look at the tools at our disposal.

### [The Django test client](https://docs.djangoproject.com/en/6.0/intro/tutorial05/#the-django-test-client)

> Django provides a test [Client](https://docs.djangoproject.com/en/6.0/topics/testing/tools/#django.test.Client) to simulate a user interacting with the code at the view level. We can use it in `tests.py` or even in the `shell`.

> We will start again with the shell, where we need to do a couple of things that won’t be necessary in tests.py. The first is to set up the test environment in the shell:

```bash
python manage.py shell
```

```bash
>>> from django.test.utils import setup_test_environment
>>> setup_test_environment()
```

> Next we need to import the test client class (later in tests.py we will use the django.test.TestCase class, which comes with its own client, so this won’t be required):

```bash
>>> from django.test import Client
>>> # create an instance of the client for our use
>>> client = Client()
```

> With that ready, we can ask the client to do some work for us:

```bash
>>> # get a response from '/'
>>> response = client.get("/")
Not Found: /
>>> # we should expect a 404 from that address; if you instead see an
>>> # "Invalid HTTP_HOST header" error and a 400 response, you probably
>>> # omitted the setup_test_environment() call described earlier.
>>> response.status_code
404
>>> # on the other hand we should expect to find something at '/polls/'
>>> # we'll use 'reverse()' rather than a hardcoded URL
>>> from django.urls import reverse
>>> response = client.get(reverse("polls:index"))
>>> response.status_code
200
>>> response.content
b'\n    <ul>\n    \n        <li><a href="/polls/1/">What&#x27;s up?</a></li>\n    \n    </ul>\n\n'
>>> response.context["latest_question_list"]
<QuerySet [<Question: What's up?>]>
```

### [Improving our view](https://docs.djangoproject.com/en/6.0/intro/tutorial05/#improving-our-view)

> The list of polls shows polls that aren’t published yet (i.e. those that have a `pub_date` in the future). Let’s fix that.

> `Question.objects.filter(pub_date__lte=timezone.now())` returns a queryset containing `Questions` whose `pub_date` is less than or equal to - that is, earlier than or equal to - `timezone.now()`.

### [Testing our new view](https://docs.djangoproject.com/en/6.0/intro/tutorial05/#testing-our-new-view)

> Now you can satisfy yourself that this behaves as expected by firing up `runserver`, loading the site in your browser, creating a few `Question` entries with dates in the past and future, and checking that only those that have been published are listed. You don’t want to have to do that every single time you make any change that might affect this - so let’s also create a test, based on our `shell` session above.

We have created a shortcut function to create questions as well as a new test class. Check out updated `polls/tests.py`.
Let’s look at some of these more closely.

- First is a question shortcut function, `create_question`, to take some repetition out of the process of creating questions.
- `test_no_questions` doesn’t create any questions, but checks the message: “No polls are available.” and verifies the `latest_question_list` is empty. Note that the `django.test.TestCase` class provides some additional assertion methods. In these examples, we use `assertContains()` and `assertQuerySetEqual()`.
- In `test_past_question`, we create a question and verify that it appears in the list.
- In `test_future_question`, we create a question with a `pub_date` in the future. The database is reset for each test method, so the first question is no longer there, and so again the index shouldn’t have any questions in it.

### [Testing the DetailView](https://docs.djangoproject.com/en/6.0/intro/tutorial05/#testing-the-detailview)

- Added `get_queryset` in `polls/views.py`.
- Added tests, to check that a Question whose `pub_date` is in the past can be displayed, and that one with a `pub_date` in the future is not. Check out `polls/tests.py`. Look at `QuestionDetailViewTests` class.

### [Ideas for more tests](https://docs.djangoproject.com/en/6.0/intro/tutorial05/#ideas-for-more-tests)
