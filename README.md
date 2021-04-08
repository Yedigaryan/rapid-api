# rapid-api
Django REST API

# Prepare for deployment to Heroku
Create a Git repository wherever you like, and commit your work thus far. We’ll need the repo’s access details later in order to push our API to Heroku.

# Procfile (procfile)
Create the file procfile in the root of your repo. It will tell Heroku this is a web dyno that needs to run gunicorn, along with the location of our wsgi entry point. Pasting the following line into the body of the file will get ‘er done:

web: gunicorn rapidapipractice.wsgi --log-file -
Database configuration (settings.py)
We’re going to set up the hobby tier of Heroku postgres, because it’s free, Django supports it, and it gets added automatically. Moreover, we will have to move away from the default SQLite db that Django comes with out of the box, because Heroku won’t support a file-based db. It will simply delete it every time the app restarts.

Open /api/settings.py and copy the following configuration into the bottom of the file:

# Heroku: Update database configuration from $DATABASE_URL. 
import dj_database_url 
db_from_env = dj_database_url.config(conn_max_age=500) DATABASES['default'].update(db_from_env)
Requirements (requirements.txt)
Heroku will read your requirements.txt file in order to learn what it needs to make available in your production environment. It should list the following (versions may vary):

Django==2.2.5
djangorestframework==3.10.3
pytz==2019.2
sqlparse==0.3.0
dj-database-url==0.5.0
gunicorn==19.9.0 
psycopg2-binary==2.7.7
Note: it is generally recommended that even though we won’t be using most of these modules locally, we should go ahead and install them in our virtual env, so we can simply run `pip freeze` to generate a complete and versioned list of dependencies that Heroku will need. That said, you can also just paste the above into a file that you create in the root of your repo called requirements.txt.

Runtime (runtime.txt)
Next, we’ll need to create our runtime.txt file in our root directory. It just tells Heroku what language and the version we’re using.

python-3.7.0
We should now be ready to start deploying our API on Heroku.

# How to Publish Your API on Heroku
1. Sign up for a Heroku account
Heroku is a really neat service that offers free hosting for hobby tier projects. You can create a free account at  www.heroku.com.

2. Install the Heroku client locally
Follow these instructions to download and install the client locally.

3. Login to the Heroku CLI
heroku login
log in to the heroku cli
Heroku will then provide a link to a login GUI, where you can sign in.

4. Create and upload the app
If you haven’t yet, you will need to create a Git repository and commit your work.

To create the app we run the “create” command in the root directory of our git repository. This creates a git remote (“pointer to a remote repository”) named heroku in our local git environment.

heroku create rapid-api-practice
Django Rest Framework has a nice GUI out of the box, but since we’re going to be adding this to RapidAPI, we’ll just skip out on serving the static files associated with the project (i.e. CSS).

heroku config:set DISABLE_COLLECTSTATIC=1
We can then push our app to the Heroku repository as shown below. This will upload the app, package it in a dyno, run collectstatic, and start the site.

git push heroku master
This will generate the URL that we will have to navigate to in order to view our app. We will then have to add that URL to Django’s list of accepted hosts. So, let’s go ahead and dd Heroku address (i.e. `rapid-api-practice.herokuapp.com`) to ALLOWED_HOSTS in Settings.py

Although the API should now be “running,” we’ll need to migrate our models to set up our database.

heroku run python manage.py migrate
Next, let’s have Heroku create our administration superuser:

heroku run python manage.py createsuperuser
Now, we should be able to look at the site. It should work, although the only record will be the superuser we just created:

heroku open
Create some users, groups, and albums in the admin site, and check out whether the site is behaving as you expect.

5. Managing addons
In order to view the add-ons associated with your app, use the heroku addons command. This will list all addons, and their price tier and state:

(env) jdv@jdv-Inspiron-5559 ~/rapid-api-practice $ /snap/bin/heroku addons
Add-on                                            Plan       Price  State  
───────────────-            ─────  ─────  ────
heroku-postgresql (postgresql-rectangular-94269)  hobby-dev  free   created
 └─ as DATABASE
 
The table above shows add-ons and the attachments to the current app (rapid-api-practice) or other apps.
6. Setting configuration variables
You can view your configuration variables for your app by running the heroku config command. Below you can see that we have two variables, the DATABASE_URL used to configure our database, and DISABLE_COLLECT_STATIC, which is telling Heroku that it doesn’t need to worry about serving CSS.

> heroku config === api Config Vars DATABASE_URL: postgres://uzfnbcyxidzgrl:j2jkUFDF6OGGqxkgg7Hk3ilbZI@ec2-54-243-201-144.compute-1.amazonaws.com:5432/dbftm4qgh3kda3
DISABLE_COLLECT_STATIC=1
We’ll have to set our Django secret key to something really secret, and we’ll need to set DEBUG to False in settings.py, so we don’t leak any private tracebacks to the viewing public.

Once those changes are made, we can rebuild our app by running:

git push heroku master
7. Add our app to RapidAPI
Alright, y’all homestretch. Now that we have our URL, we can go on over to RapidAPI and add our app to the marketplace. Click My APIs. rapidapi my apis
Click Add New API in the left sidebar.

rapidapi add new api
At this point, we will have the option to import a Swagger file, or to manually define our endpoints and such. To keep things simple, we’ll do it manually for now.

rapidapi add api
Finally, we will need to follow the following steps. This should only take a few minutes.

get your API ready to be published on RapidAPI
And shazzam! We have created a Django API with full CRUD functionality, deployed it to Heroku and added it to the RapidAPI Marketplace!!

Next Steps:

Explore RapidAPI to beef up the functionality of your new API.
