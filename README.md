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
