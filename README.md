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

Look at example of `index()` view,  which displays the latest 5 poll questions in the system, separated by commas, according to publication date:

```python
#polls/views.py
def index(request):
    latest_question_list = Question.objects.order_by("-pub_date")[:5]
    output = ", ".join([q.question_text for q in latest_question_list])
    return HttpResponse(output)
```

Next, create a directory called `templates` in your `polls` directory. Django will look for templates in there.

> Your project’s `TEMPLATES` setting describes how Django will load and render templates. The default settings file configures a `DjangoTemplates` backend whose `APP_DIRS` option is set to `True`. By convention `DjangoTemplates` looks for a “templates” subdirectory in each of the `INSTALLED_APPS`.

> Within the `templates` directory you have just created, create another directory called `polls`, and within that create a file called `index.html`. In other words, your template should be at `polls/templates/polls/index.html`. Because of how the `app_directories` template loader works as described above, you can refer to this template within Django as `polls/index.html`.

> **Template namespacing.** Now we might be able to get away with putting our templates directly in `polls/templates` (rather than creating another `polls` subdirectory), but it would actually be a bad idea. Django will choose the first template it finds whose name matches, and if you had a template with the same name in a different application, Django would be unable to distinguish between them. We need to be able to point Django at the right one, and the best way to ensure this is by namespacing them. That is, by putting those templates inside another directory named for the application itself.

Make template in `polls/templates/polls/index.html`.

Look at updated code below which loads the template called `polls/index.html` and passes it a context. The **context** is a dictionary mapping template variable names to Python objects.

```python
#polls/views.py
from django.template import loader


def index(request):
    latest_question_list = Question.objects.order_by("-pub_date")[:5]
    template = loader.get_template("polls/index.html")
    context = {"latest_question_list": latest_question_list}
    return HttpResponse(template.render(context, request))
```

## [A shortcut: **render()**](https://docs.djangoproject.com/en/6.0/intro/tutorial03/#a-shortcut-render)

It’s a very common idiom to load a template, fill a context and return an `HttpResponse` object with the result of the rendered template. Django provides a shortcut. Look at full `index()` view in `polls/views.py`.

The `render()` function takes the request object as its first argument, a template name as its second argument and a dictionary as its optional third argument. It returns an `HttpResponse` object of the given template rendered with the given context.

## [Raising a 404 error](https://docs.djangoproject.com/en/6.0/intro/tutorial03/#raising-a-404-error)

Let's make the page that displays the question text for a given poll and raises the [Http404](https://docs.djangoproject.com/en/6.0/topics/http/views/#django.http.Http404) exception if a question with the requested ID doesn’t exist:

```python
from django.http import Http404


def detail(request, question_id):
    try:
        question = Question.objects.get(pk=question_id)
    except Question.DoesNotExist:
        raise Http404("Question does not exist")
    return render(request, "polls/detail.html", {"question": question})
```

## [A shortcut: **get_object_or_404()**](https://docs.djangoproject.com/en/6.0/intro/tutorial03/#a-shortcut-get-object-or-404)

It’s a very common idiom to use `get()` and raise `Http404` if the object doesn’t exist. Django provides a shortcut. Look at final verison `detail` view in `polls/views.py`.

> The `get_object_or_404()` function takes a Django model as its first argument and an arbitrary number of keyword arguments, which it passes to the `get()` function of the model’s manager. It raises `Http404` if the object doesn’t exist.

> There’s also a `get_list_or_404()` function, which works just as `get_object_or_404()` – except using `filter()` instead of `get()`. It raises `Http404` if the list is empty.

It's better to use `get_object_or_404()` because it reduces coupling between the model and view layers, without requiring you to import `Http404` into your models or catch `ObjectDoesNotExist` manually in every view.

## [Use the template system](https://docs.djangoproject.com/en/6.0/intro/tutorial03/#use-the-template-system)

Check updated version of `polls/templates/polls/detail.html`.

> The template system uses dot-lookup syntax to access variable attributes. In the example of `{{ question.question_text }}`, first Django does a dictionary lookup on the object question. Failing that, it tries an attribute lookup – which works, in this case. If attribute lookup had failed, it would’ve tried a list-index lookup.

> Method-calling happens in the {% for %} loop: question.choice_set.all is interpreted as the Python code question.choice_set.all(), which returns an iterable of Choice objects and is suitable for use in the {% for %} tag.

See the [template guide](https://docs.djangoproject.com/en/6.0/topics/templates/) for more about templates.

### The `_set` suffix in Django

Is automatically generated as the default `RelatedManager` name for **reverse foreign key relationships**.

Example:

```python
from django.db import models

class Author(models.Model):
    name = models.CharField(max_length=100)

class Book(models.Model):
    title = models.CharField(max_length=100)
    author = models.ForeignKey(Author, on_delete=models.CASCADE)

# Django automatically adds book_set to the Author model
author = Author.objects.get(id=1)
books = author.book_set.all()  # Auto-generated reverse manager
```

You can override the automatic _set name using the related_name parameter:

```python
class Book(models.Model):
    title = models.CharField(max_length=100)
    author = models.ForeignKey(
        Author, 
        on_delete=models.CASCADE,
        related_name='books'  # Now use author.books instead of author.book_set
    )

# Now you would use
author.books.all()  # Custom name, no _set
```

Key points:
- `_set` is **only for** reverse `ForeignKey` relationships (not `ManyToManyField`).
- It uses the *lowercase* name of the related model.
- It's `automatically created` if you don't specify `related_name`.
- You can always customize it to something more readable.

## [Removing hardcoded URLs in templates](https://docs.djangoproject.com/en/6.0/intro/tutorial03/#removing-hardcoded-urls-in-templates)

In the `polls/index.html` template, the link was partially hardcoded like this:

```html
<li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
```

> The problem with this hardcoded, tightly-coupled approach is that it becomes challenging to change URLs on projects with a lot of templates. However, since you defined the name argument in the `path()` functions in the `polls.urls` module, you can remove a reliance on specific URL paths defined in your url configurations by using the `{% url %}` **template tag**:

```html
<li><a href="{% url 'detail' question.id %}">{{ question.question_text }}</a></li>
```

You can see exactly where the URL name of ‘detail’ is defined below:

```python
...
# the 'name' value as called by the {% url %} template tag
path("<int:question_id>/", views.detail, name="detail"),
...
```

If you want to change the URL of the polls detail view to something else:
```python
...
# added the word 'specifics'
path("specifics/<int:question_id>/", views.detail, name="detail"),
...
```

## [Namespacing URL names](https://docs.djangoproject.com/en/6.0/intro/tutorial03/#namespacing-url-names)

