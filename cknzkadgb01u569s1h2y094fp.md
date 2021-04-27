## Deploying Django to Heroku

In the previous [Django - Getting Started](https://blog.benhammond.tech/django-getting-started) post, we set up a new Django app, basically creating a fullstack Python `Hello World`.

In this post, we will deploy this very basic app to Heroku (a free hosting platform which can run a Django server among many other types of web services). There are _many_ ways to deploy Django, but not a single tutorial I've found online actually worked for me as written. Therefor, I've collected the process that finally did the trick and have outlined it below. A lot of the process comes from our bootcamp instructors, with some customization found several layers deep on Stack Overflow and Reddit (and eventually some personal manipulation). Please let me know if this works for you, and comment below if you have any questions!

# Deploy Process

## Get Ready
- Load up your terminal and `cd` into the floor of your Django project. Running the command `ls` should your `requirements.txt`, `manage.py`, `main_app` folder, and `your-app_project`, amongst several others. My example is below (some of these files will be created in the following steps)

![Screen Shot of terminal showing example files at floor level](https://cdn.hashnode.com/res/hashnode/image/upload/v1619496779439/qetCJyLwD.png)

## Log In With Heroku CLI

You will need to have a Heroku account for this process. It's [free to sign up](https://signup.heroku.com/), and you should also [install the CLI](https://devcenter.heroku.com/articles/heroku-cli#download-and-install) (command line interface) from Heroku to complete the following steps: 

- `heroku login` and press any key to launch a browser window which will allow you to sign in to Heroku

## Procfile
This file will tell Heroku what actions to take when it receives a web request:
- create a blank file with _no file extension_: `touch Procfile`
> ensure the capitalization is exact on Procfile; if you accidentally mixed the casing up you can fix it using [this method I wrote about](https://blog.benhammond.tech/renaming-your-github-projects)
- open the new `Procfile` in VSCode, add and save this line (subbing in your own project folder name; the one that ends with `_project`):
```
web: gunicorn your-app_project.wsgi
```

## Runtime
This file simply tells Heroku which exact version of Python to use:
- `python3 --version` displays your current version of Python locally
- copy that exact series of numbers; mine is _**3.9.2**_
- `touch runtime.txt` and open it in VSCode
- type `python-` and then paste (no space in between) the exact version. Mine ends up as _**python-3.9.2**_

## More Packages
Why re-invent the wheel? Someone already got all this cool stuff working; use it!
- `pip3 install django-on-heroku`
- `pip3 install gunicorn`
- `pip3 install whitenoise`
- open **settings.py**
- add `import django_on_heroku` near the top of your file under your other import statements
- scroll the very bottom of **settings.py**
- add `django_on_heroku.settings(locals())`

## Update Dependencies
- `pip3 freeze > requirements.txt`

## Deploy
- `heroku create your-app --buildpack heroku/python`
> This will try and create a Heroku project with the URL **your-app.herokuapp.com**. If someone has already taken your preferred name you can change to anything else that you want
- `heroku addons:create heroku-postgresql:hobby-dev` - adds a free postgreSQL database to your free Heroku account


## Set Heroku Config Vars 

> In [the previous post](https://blog.benhammond.tech/django-getting-started), we set our app's local config vars in an .env file and read them out dynamically. Please ensure your app is set up properly to use the following commands and allow Heroku to store its own config vars

- `heroku config:set DISABLE_COLLECTSTATIC=1` - prevents a problem I couldn't figure out
- `heroku config:set DATABASE_NAME=your-app` - add your actual app name (the part before the `_project`)
- `heroku config:set DEBUG=False`
- `heroku config:set SECRET_KEY=whatever_your_secret_key_is` 

## Push to Production

- `git add .`
- `git commit -m "fingers crossed"`
- `git push heroku main` - this will push your app up to the newly created Heroku project, rather than pushing to GitHub or some other remote as you normally would with _git push origin main_
> If you are still using `master` branch instead of `main`, you'll want to swap that word, and it **must** be from one of those two named branches that you initiate the push

## Fix Deployed Database
Now, you'll tunnel in to the Heroku server with your command line, so you can see what's going on in there and make needed updates to the free database they gave you.
- `heroku run bash`
- once inside, run `python3 manage.py makemigrations`. As before, there might not be any changes after this step
- `python3 manage.py migrate` - this brings your database up to date with the built-in user models that Django provides
- `exit` gets you out of Heroku and back on to your local machine

## See If It Works!

- `heroku open` will launch the site in a browser.
> If you have problems, typing `heroku logs --tail` will let your terminal log all sorts of difficult to decipher messages, including some that will help you figure out what's wrong. To exit this logging feature, use `CTRL+C`

<iframe src="https://giphy.com/embed/dIxkmtCuuBQuM9Ux1E" width="480" height="240" frameBorder="0" class="giphy-embed" allowFullScreen></iframe>


Photo by <a href="https://unsplash.com/@frostroomhead?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Rodion Kutsaev</a> on <a href="https://unsplash.com/s/photos/stairs?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>
  










