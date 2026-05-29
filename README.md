# [Part 3: Views and templates](https://docs.djangoproject.com/en/6.0/intro/tutorial03/)

## [Overview](https://docs.djangoproject.com/en/6.0/intro/tutorial03/#overview)

A view is a “type” of web page in your Django application that generally serves a specific function and has a specific template. Examples you can find in [original documantation](https://docs.djangoproject.com/en/6.0/intro/tutorial03/#overview).

> A URL pattern is the general form of a URL - for example: `/newsarchive/<year>/<month>/`.
To get from a URL to a view, Django uses what are known as ‘URLconfs’. A URLconf maps URL patterns to views.
This tutorial provides basic instruction in the use of URLconfs, and you can refer to [URL dispatcher](https://docs.djangoproject.com/en/6.0/topics/http/urls/) for more information.

## [Writing more views](https://docs.djangoproject.com/en/6.0/intro/tutorial03/#writing-more-views)

New veiws (`detail`, `results`, `vote`) were added in `polls/views.py`. Also new urls in `polls/urls.py`.

Take a look in your browser, at `/polls/34/`. It’ll run the `detail()` function and display whatever `ID` you provide in the URL. Try `/polls/34/results/` and `/polls/34/vote/` too – these will display the placeholder results and voting pages.

```python
detail(request=<HttpRequest object>, question_id=34)
```

The `question_id=34` part comes from `<int:question_id>`. Using angle brackets *“captures”* part of the URL and sends it as a keyword argument to the view function. The `question_id` part of the string defines the name that will be used to identify the matched pattern, and the `int` part is a converter that determines what patterns should match this part of the URL path. The colon (`:`) separates the converter and pattern name.

## [Write views that actually do something](https://docs.djangoproject.com/en/6.0/intro/tutorial03/#write-views-that-actually-do-something)

> Each view is responsible for doing one of two things: returning an [HttpResponse](https://docs.djangoproject.com/en/6.0/ref/request-response/#django.http.HttpResponse) object containing the content for the requested page, or raising an exception such as [Http404](https://docs.djangoproject.com/en/6.0/topics/http/views/#django.http.Http404).

Look at example in `polls/views.py` in `index()` view,  which displays the latest 5 poll questions in the system, separated by commas, according to publication date. We commented it as `first vesion`.

Next, create a directory called `templates` in your `polls` directory. Django will look for templates in there.

> Your project’s `TEMPLATES` setting describes how Django will load and render templates. The default settings file configures a `DjangoTemplates` backend whose `APP_DIRS` option is set to `True`. By convention `DjangoTemplates` looks for a “templates” subdirectory in each of the `INSTALLED_APPS`.

> Within the `templates` directory you have just created, create another directory called `polls`, and within that create a file called `index.html`. In other words, your template should be at `polls/templates/polls/index.html`. Because of how the `app_directories` template loader works as described above, you can refer to this template within Django as `polls/index.html`.

> **Template namespacing.** Now we might be able to get away with putting our templates directly in `polls/templates` (rather than creating another `polls` subdirectory), but it would actually be a bad idea. Django will choose the first template it finds whose name matches, and if you had a template with the same name in a different application, Django would be unable to distinguish between them. We need to be able to point Django at the right one, and the best way to ensure this is by namespacing them. That is, by putting those templates inside another directory named for the application itself.

Make template in `polls/templates/polls/index.html`.

Look at updated code in `polls/views.py`. That code loads the template called `polls/index.html` and passes it a context. The **context** is a dictionary mapping template variable names to Python objects. We commented it as `second vesion`.

## [A shortcut: **render()**](https://docs.djangoproject.com/en/6.0/intro/tutorial03/#a-shortcut-render)

It’s a very common idiom to load a template, fill a context and return an `HttpResponse` object with the result of the rendered template. Django provides a shortcut. Look at full `index()` view in `polls/views.py`.

The `render()` function takes the request object as its first argument, a template name as its second argument and a dictionary as its optional third argument. It returns an `HttpResponse` object of the given template rendered with the given context.

## [Raising a 404 error](https://docs.djangoproject.com/en/6.0/intro/tutorial03/#raising-a-404-error)
