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