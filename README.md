# Django Tutorial
I am doing the Django tutorial so that I can get a position as a full-stack software engineer. [Tutorial](https://docs.djangoproject.com/en/4.0/intro/tutorial01/)

## Part 1
### Creating python virtualenv
To prevent dependency conflicts, a virtual environment is recommended. Using pipenv, create a virtual environment using `pipenv install django`. If you encounter a `--system` error, you need to escape the virutal environment that you are currently in. One nasty way to solve this (that does work) is deleting the `.venv` directory.
### Creating a project
A project can be created using the command:  
`django-admin startproject mysite`  
Note that this command will create a new directory for a project and then put the project files in that directory. To create a project in the same directory, append a "." at the end like so:  
`django-admin startproject mysite .`

### Running the Server
The command to start the server is:  
`python manage.py runserver`  
Note that you there may be a warning to run  
`python manage.py migrate`  
which you should do if the warning exists.

### Creating the polls app
To create the app, make sure we are in the same directory as **manage.py** and type:  
`python manage.py startapp polls`  
What is the difference between a project and an app? An app is a web applicaiton that does something e.g., a blog system, a database of public records or a small poll app. A project is a collection of configuration and apps for a particular website. A project can contain multiple apps. An app can be in multiple projects.

### Write your first view
In the **polls/views.py**:  

    from django.http import HttpResponse


    def index(request):
        return HttpResponse("Hello, world. You're at the polls index.")

Create a file called **polls/urls.py** file with the following code:  

    from django.urls import path

    from . import views

    urlpatterns = [
        path('', views.index, name='index'),
    ]

Now we point the root of URLconf at the polls.urls module. In **mysite/urls.py**, add an import for **django.urls.include** and insert an include() in the urlpatterns list, so you have in **mysite/urls.py**: 

    from django.contrib import admin
    from django.urls import include, path

    urlpatterns = [
        path('polls/', include('polls.urls')),
        path('admin/', admin.site.urls),
    ]
Verify that the app is working by going to `http://localhost:8000/polls/` after running the runserver command `python manage.py runserver`.

If you get an error like me, make sure you do the steps that you document instead of just writing it in the readme, like a certain web developer (Not Me).

Okay, it was me.

## Part2

### Database setup
In **mysite/settings.py** we may decide to change the database type. None of it really applies to us (yet), but it may soon.

### Creating models

A model is the single, definitive source of information about your data. It contains the essential fields and behaviors of the data you're storing. In the **polls/models.py** we create two models:
    from django.db import models


    class Question(models.Model):
        question_text = models.CharField(max_length=200)
        pub_date = models.DateTimeField('date published')


    class Choice(models.Model):
        question = models.ForeignKey(Question, on_delete=models.CASCADE)
        choice_text = models.CharField(max_length=200)
        votes = models.IntegerField(default=0)

### Activating Models
We need to tell our project that the **polls** app is installed. In **mysite/settings.py**


    INSTALLED_APPS = [
        'polls.apps.PollsConfig',
        'django.contrib.admin',
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.messages',
        'django.contrib.staticfiles',
    ]
Now we need to run:
    python manage.py makemigrations polls

By running **makemigrations**, you're telling Django that you've made some changes to your models (in this case, you've made new ones) and that you'd like the changes to be stored as a migration.  
The **sqlmigrate** command takes migration names and returns their SQL:
    python manage.py sqlmigrate polls 001
    python manage.py migrate

### Introducting the Django Admin
Generating admin sites is pretty easy, so Django automates this.

## Part 3

### Writing more views
Now let's add a few more views to **polls/views.py**. These views are slightly different, because they take an argument:

    def detail(request, question_id):
        return HttpResponse("You're looking at question %s." % question_id)

    def results(request, question_id):
        response = "You're looking at the results of question %s."
        return HttpResponse(response % question_id)

    def vote(request, question_id):
        return HttpResponse("You're voting on question %s." % question_id)

We wire these new views into the **polls.urls** module by adding the following **path()** calls:
from django.urls import path

    from . import views

    urlpatterns = [
        # ex: /polls/
        path('', views.index, name='index'),
        # ex: /polls/5/
        path('<int:question_id>/', views.detail, name='detail'),
        # ex: /polls/5/results/
        path('<int:question_id>/results/', views.results, name='results'),
        # ex: /polls/5/vote/
        path('<int:question_id>/vote/', views.vote, name='vote'),
    ]
### Write views that actually do something
Each view is responsible for doing one of two things: returning an **HttpResponse** object containing the content for the requested page, or raising an exception such as **Http404**. The rest is up to you.  

Your view can read records from a database, or not. It can use a template system such as Django's - or a third-party Python template system - or not.  

All Django wants is that **HttpResponse**. Next we create the folders **templates/polls** and within that we create the file **index.html**.
    {% if latest_question_list %}
        <ul>
        {% for question in latest_question_list %}
            <li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
        {% endfor %}
        </ul>
    {% else %}
        <p>No polls are available.</p>
    {% endif %}
Now we update our index view in the **polls/views.py** to use the template:
    from django.http import HttpResponse
    from django.template import loader

    from .models import Question


    def index(request):
        latest_question_list = Question.objects.order_by('-pub_date')[:5]
        template = loader.get_template('polls/index.html')
        context = {
            'latest_question_list': latest_question_list,
        }
        return HttpResponse(template.render(context, request))

The code loads the template called polls/index.html and passes it to a context.

### A shortcut: render()
It's a very common idiom to load a template, fill a context and return an HttpResponse object with the result of the rendered template. Django provides a shortcut. Here's the full **index()** view, rewritten.

    from django.shortcuts import render

    from .models import Question


    def index(request):
        latest_question_list = Question.objects.order_by('-pub_date')[:5]
        context = {'latest_question_list': latest_question_list}
        return render(request, 'polls/index.html', context)

The **render()** function takes the request object as its first argument, a template name as its second argument and a dictionary as its third argument. It returns an **HttpResponse** object of the given template rendered with the given context.

### Raising a 404 error
Sometimes we want a view to raise a Http404 exception if a question with the requested id doesn't exist. We can use try/except or the **get_object_or_404()** method. Using the method is preferred because the try/exception would "couple the model layer to the view layer", which is undesired according to the design priniciples of Django.

### Use the template system

The template system uses dot-lookup syntax to access the variable attributes. In the example of **{{ question.question_text}}**, first Django does a dictionary lookup on the object **question**. Failing that, it tries an attriubute lookup. If the attribute lookup failed, it would have tried a list-index lookup. Method-calling happens in the {% for %} loop: question.choice_set.all is interpreted as the Python code question.choice_set.all(), which returns an iterable of Choice objects and is suitable for use in the {% for %} tag.

### Removing hardcoded URLs in templates

The problem with hardcoded, tightly-coupled approach is that it becomes challenging to change URLs on projects with a lot of templates. However, since you defined the name argument in the **path()** functions in the polls.urls module, you can remove a reliance on specific URL paths defined in your url configurations by using the { % url %} template tag.
    <li><a href="{% url 'detail' question.id %}">{{ question.question_text }}</a></li>
The way this works is by lookin gup the URL definition as specified in the polls.urls module. You can see exactly where the URL name of 'detail' is defined below:
    path('<int:question_id>/',views.detail, name='detail'),
If you want to change the URL of the polls detail view to something else, perhaps to something like polls/specifics/12/ instead of doing it in the template (or templates) you would change it in **polls/urls.py**.

### Namespacing URL names
The tutorial project has just one app, polls. In real Django projects, there might be more. In order to identify which app view to create for a url when using the {% url % } template tag, we add namespaces to the URLconf. In the **polls/urls.py** file, go ahead and add an **app_name** to set the application namespace.

    from django.urls import path

    from . import views

    app_name = 'polls'
    urlpatterns = [
        path('', views.index, name='index'),
        path('<int:question_id>/', views.detail, name='detail'),
        path('<int:question_id>/results/', views.results, name='results'),
        path('<int:question_id>/vote/', views.vote, name='vote'),
    ]
When you are comfortable writing views, continue to part 4.





# Contact Me
818-518-8295  
aksgula22@gmail.com
