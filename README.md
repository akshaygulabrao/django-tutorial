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

## Running the Server
The command to start the server is:  
`python manage.py runserver`  
Note that you there may be a warning to run  
`python manage.py migrate`  
which you should do if the warning exists.



# Contact Me
818-518-8295  
aksgula22@gmail.com
