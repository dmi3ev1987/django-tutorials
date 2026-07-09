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

