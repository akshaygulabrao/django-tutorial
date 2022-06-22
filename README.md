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


# Contact Me
818-518-8295  
aksgula22@gmail.com
