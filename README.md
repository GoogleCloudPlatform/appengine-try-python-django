## Python Django Skeleton for Google App Engine

A skeleton for building Python applications on Google App Engine with the
[Django Web Framework](https://www.djangoproject.com/) using 
[Cloud SQL](https://cloud.google.com/sql/docs/introduction).

See our other [Google Cloud Platform github
repos](https://github.com/GoogleCloudPlatform) for sample applications and
scaffolding for other python frameworks and use cases.

Note that this project no lnoger uses the built-in Django library but
uses `pip -t` to vendor Django 1.8 into the lib/ folder. 

## Project Creation

The basic skeleton of this project was created using the standard Djagno commands:

    ```
    django-admin startproject myproject
    ```

The DATABASE field in the myproject/settings.py is modified to use CloudSQL locally, and through
a Unix socket when deployed to AppEngine. The static file handler is also set up 
in the app.yaml.

## Enable Cloud SQL

This project assumes you are using [CloudSQL](https://cloud.google.com/sql/docs/introduction)
as your database. CloudSQL is a managed MySQL instance hosted by Google Cloud, although you
could also host a MySQL instance elsewhere and use that. For better scalability, consider
[Cloud Datastore](https://cloud.google.com/datastore/docs/concepts/overview?hl=en), although
you must use a custom backend such as [Djangae](https://github.com/potatolondon/djangae) for your
models to work with it.

## Run Locally
1. Install the [App Engine Python SDK](https://developers.google.com/appengine/downloads).
See the README file for directions. You'll need python 2.7 and [pip 1.4 or later](http://www.pip-installer.org/en/latest/installing.html) installed too.

1. Clone this repo with

   ```
   git clone https://github.com/GoogleCloudPlatform/appengine-try-python-django.git
   ```
1. Install dependencies in the project's lib directory.
   Note: App Engine can only import libraries from inside your project directory.

   ```
   cd appengine-try-python-django
   pip install -r requirements.txt -t lib
   ```
1. Create a new CloudSQL instance. 
    * From the [Google Cloud Console](http://console.developer.google.com), go to Storage > CloudSQL> Create Instance
    * Under Acces Control > IP Address > Request IPv4 Address. This address will be your HOST for remote access to the 
      CloudSQL instance in mysite/settings.py, so replace <your-database-ip> with this address.
    * Under Databaeses, click New database and create the name for your database in mysite/settings.py. Replace
      <your-database-name> with this value.
    * Under Access Control > Authorization > Allowed Networks, click Add item, and add Network 0.0.0.0/0. This opens up
      access to your CloudSQL instance from any network. Stricter firewall settings should be considered for production
      applications.
    * Under Access Control > Users > Create user account. Create a username and password and edit settings.py DATABASES
      to reflect this. Replace <your-database-user> and <your-database-password> with these variables.

Note in myproject/settings.py, the deployed app does not use the above settings, but instead talks to the instance through a Unix
socket as root. When testing locally, use the settings created above to access the database.

1. Run this project locally from the command line:

   ```
   dev_appserver.py .
   ```

   or just using the Django local server
   ```
   python manage.py collectstatic
   python manage.py runserver
   ```

Visit the application [http://localhost:8080](http://localhost:8080)

See [the development server documentation](https://developers.google.com/appengine/docs/python/tools/devserver)
for options when running dev_appserver.

## Deploy
To deploy the application:

1. Use the [Admin Console](https://appengine.google.com) to create a
   project/app id. (App id and project id are identical)
1. Collect the static files into the static/ directory
   ```
   django-admin startproject mysite
   ```
1. Verify that <your-cloud-project-id> and <your-databas-name> have been replaced in mysite/settings.py
   with your Cloud Project ID and your database name respectively.
1. [Deploy the
   application](https://developers.google.com/appengine/docs/python/tools/uploadinganapp) with

   ```
   appcfg.py -A <your-project-id> --oauth2 update .
   ```

   or using the new [gcloud SDK](https://cloud.google.com/sdk/?hl=en) commands.
   ```
   gcloud config set project <project name> # only required once
   gcloud preview app deploy --set-default
   ```

1. Congratulations!  Your application is now live at your-app-id.appspot.com

### Installing Libraries
See the [Third party
libraries](https://developers.google.com/appengine/docs/python/tools/libraries27)
page for libraries that are already included in the SDK.  To include SDK
libraries, add them in your app.yaml file. Other than libraries included in
the SDK, only pure python libraries may be added to an App Engine project.

Alternatively, pure Python libraries can be added to requirements.txt, and
installed using `pip -t` into the lib/ folder and accessed directly through
there.

### Feedback
Star this repo if you found it useful. Use the github issue tracker to give
feedback on this repo.

## Contributing changes
See [CONTRIB.md](CONTRIB.md)

## Licensing
See [LICENSE](LICENSE)
