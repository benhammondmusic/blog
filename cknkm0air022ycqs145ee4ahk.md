## Django - Getting Started

_**This post was co-authored with fellow General Assembly Software Engineering Immersive member [JC Coles](https://jctech.blog/)**_

If you've written full stack applications in JavaScript using Node, Express, perhaps Mongoose for accessing a database, maybe some packages like Passport to help manage user authentication and authorization, etc, you quickly find yourself repeating the same setup patterns over and over. The only differences come in when you accidentally misconfigure a setting or a route and spend hours debugging. Django aims to, if not _replace_ all of those steps, at least condense those disparate processes into one. 

The following step-by-step guide will assist a beginning Python developer in launching a Django project on their local development server (and then deploying it to Heroku in the next blog post).

> If you’re starting a brand new project, skip this section. Otherwise if you are downloading someone else’s project, run the following commands instead to properly configure it on your machine. This is the equivalent of running `npm i` when freshly cloning someone else's repo. When finished, continue at step "Creating a Home Page" below
1. `python3 -m venv .env`
2. `source .env/bin/activate`
3. `pip3 install -r requirements.txt`
4. `python3 manage.py migrate`

# Django Environment Setup (New Project)

Python can and should be run in a virtual environment; this relates to the fact that your actual operating system utilizes Python code and by safely keeping your projects in a protected environment, you minimize the risk of accidentally misconfiguring your entire system. In short, you'll be simulating a mini-computer inside your computer, and running your Django project from there. You can quickly tell when your terminal is inside of the virtual environment by looking for the _(.env)_ at the beginning of your terminal prompt:

![command line prompt showing parentheses env](https://cdn.hashnode.com/res/hashnode/image/upload/v1618546676648/ES9RjRW8D.png)
 
## Virtual Environment

Create a virtual environment
- `python3 -m venv .env`

Activate it
- `source .env/bin/activate`

## Database

Create a postgres database. 
- `createdb {{{pedalcollector}}}` 

> My example is for a "pedal collector"; you can change the terms written inside triple squirrely brackets: `{{{XYZ}}}` to be whatever makes sense for your own project.

## Dependencies 

Install Django and dependencies. Psycopg2 helps Python interact with your postgres DB (similar to how Mongoose allowed Node to easily interact with MongoDB)
- `pip3 install django-extensions`
- `pip3 install Django`
- `pip3 install psycopg2`
> if that didn’t work run: `psycopg2-binary`. 

Record those settings: _requirements.text_ is the Django equivalent of your Node app's _package.json_, informing everyone what settings and packages must be configured to function.
- `pip3 freeze > requirements.txt`


 
# Project Setup

Django _projects_ can contain multiple Django _apps_. 

## New Project

Create a new project
- `django-admin startproject {{{pedalcollector}}} .` 

Make a git ignore file
- `touch .gitignore`

Specify the files and folders to ignore
- Open the blank `.gitignore` file in VSCode
- add the line `.env`

> For a comprehensive `.gitignore` file like you might get from a create-react-app, you can visit [gitignore.io](https://gitignore.io) and select _Django_. This will generate a complete file for you: [click to view Django .gitignore](https://www.toptal.com/developers/gitignore/api/django)

## Main App

Create a second app within this project called **main_app**
- `python3 manage.py startapp main_app`

> If you're having issues, make sure:
- you included the ` .` at the end of the start project command
- you might need to use the command `python` instead of `python3`; this seemed to occur on newer Macs or machines where `python --version` and `python3 --version` both referenced the same release of Python
 
Let Django know about your 2nd app:
- Open the project in VSCode: `code .`
- Open your project settings, mine is: `pedalcollector_project/settings.py`
- Scroll down to `Installed_Apps` and add `main_app,` as the new first element in the array. Remember the comma!
![Adding main_app screenshot](https://cdn.hashnode.com/res/hashnode/image/upload/v1618548443970/aAONZQhrq.png)

## Development Server

Start up the development server on your local machine
- `python3 manage.py runserver`
- Open a browser and visit [localhost:8000](http://localhost:8000] to see your site (well, just the fun rocket for now... 🚀)

## Development Database

Configure project for postgres by changing the final property of your engine value to `.postgresql`,  and adding your project name to the ‘NAME’ entry:
```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'pedalcollector',
    }
}
```

## Migrations

Stage the db migrations (kind of like `git add .` but for changes to the database schema as the app evolves over time
- `python3 manage.py makemigrations`
Make those migrations (kind of like `git commit` but you don't have to rack your brain for a meaningful commit message)
- `python3 manage.py migrate`

Restart your server (you can do this regularly while debugging)
- kill the process using `<CONTROL> + C`
- start it again: `python3 manage.py runserver`
 

# Creating a Home Page
 
## Add a `home` route

Routes are defined in the `urls.py` file(s); you _could_ add all of your routes to `{{{YOURPROJECT}}}/urls.py`, but it is best practice for each Django app to define its own and include a link to the new URLs file in the project's URLconf:

- `touch main_app/urls.py`
- Open the newly created file in VSCode
- Set up the imports and basic route using the code below:

```
from django.urls import path
from . import views

urlpatterns = [
    path('', views.home, name='home'),
    # more routes will go here
]
```

> Using an empty string `""` as the first parameter in `path()` is telling Django to route the base URL with no added paths to the home view (e.g. `your-project.com/` Later we will add more routes, for example using `"about/"` as the first argument in the new route, and linking that to an `about` page, which the user will access by going to `your-project.com/about`

Now, include this new file in the existing project’s urls.py
- Open `{{{YOUR_PROJECT}}}/urls.py` (on mine it is `pedalcollector/urls.py`)
- Add a new item to the `urlpatterns` array, making sure the items are separated by commas
- Also you will need to import the `include` function before calling it in the array
- It will end up looking like this:

```
from django.contrib import admin
# from django.urls import path
# add include to your imports from django.urls
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    # add this line below
    path('', include('main_app.urls')),
]
```

## Add a home view

Now that the home _route_ knows where to direct the client, you need to make a home _view_ to be displayed there. Starting very simply, you'll create the `home.html` file
- `touch main_app/templates/home.html` 
- Open `home.html` in VSCode and paste in some valid HTML as a starting point:

```
<!DOCTYPE html>
<html>
    <head>

    </head>
    <body>
        <h1>Home</h1>
    </body>
</html>
```

Back in your browser, visit [localhost:8000](http://localhost:8000) and refresh to view your sweet new home page. If it doesn't work, remember to restart your Django server:
- in terminal: `<CONTROL> + C`
- `python3 manage.py runserver`

# Done!

You should now have a functioning (very simple) Django app within your project, viewable on your local machine. To continue building, follow the next steps:
- Adding more routes, templates, and rendered data _(blog post currently being written)_
- Reading from your database _(blog post currently being written)_
- Deploying your Django app on Heroku _(blog post currently being written)_
 

__Photo by <a href="https://unsplash.com/@tateisimikito?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Jukan Tateisi</a> on <a href="https://unsplash.com/collections/1342728/climbing?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>__
  


