# Makeability Lab Website
The Makeability Lab is a an HCI/Ubicomp research lab at the University of Washington directed by Professor Jon Froehlich. Founded in 2012 by Froehlich and students, the Makeability Lab focuses on designing and studying novel interactive experiences that cross between bits and atoms—the virtual and the physical—and back again to confront some of the world's greatest challenges in environmental sustainability, health and wellness, education, and universal accessibility. This repository contains the Makeability Lab's Django 1.9 (python 3.5) based site.

# Docker Installation
TODO Aileen: Write installation instructions for Docker-based dev install.

# Deploying to Production
The Makeability Lab website auto-deploys from GitHub to the department's Docker infrastructure using webhooks:
![webhooks_screenshot](https://github.com/jonfroehlich/makeabilitylabwebsite/blob/master/media/readme/webhooks_screenshot.png "Webhooks Screenshot"). When we push new code to github, the new code will auto-deploy to makeabilitylab-test. When we are ready to push changes to production, we need to do the following:
```
git tag <my version number>
git push --tags
```
This will cause that tag to deploy to production. 

## Configuring the Production Server
The production server was configured largely by UW CSE's Jason Howe. Note that settings.py reads in a config.ini file to configure a connection to the PostgreSQL database. This config.ini file is *not* in git (for obvious reasons as it contains secret keys and passwords). Thus, Jason has setup a "volume mount" for this file so that the production Docker session can read that file.

# Makeability Lab Data
There are two types of Makeability Lab data: (i) uploaded files like PDFs, PowerPoint files, images, etc. and (ii) data that goes into the database (SQLite in local dev, PostgreSQL on production).

## Uploaded Files
All data/files uploaded to the Makeability Lab website via the admin interface (e.g., talks, publications) goes into the /media folder. Although typically you will not ever need to manually access this folder, you can do so by ssh'ing into recycle.cs.washington.edu and cd to /cse/web/research/makelab/www. This files area is being mapped into the /media folder.

## Access to Production Database Server
The Makeability Lab website uses PostgreSQL on production, which is running on grabthar.cs.washington.edu. In the (extremely) rare instance that you need to access Postgres directly, you must do so via recycle.cs.washington.edu.



# Manual Installation
We strongly advise running the Docker-based installation since that's what the department mandates and you get an instantly configured dev environment for free. However, if you want the *pain* :) of manually installing all of the required libs, then this is how you should do it. The full step-by-step instructions for installation are [here](https://docs.google.com/document/d/149S_SHOzkJOhNHU4ENMU0YCbk-Vy92y5ZSTeCXG9Bhc/edit) (invite only for now).

## Requirements
* Python 3.51
* Pip 3
* Django 1.9
* ImageMagick
* ghostscript
* django_extensions (pip)
* Pillow (pip)
* Wand (pip)
* bibtex parser (pip)
* xmltodict (pip)
* django-image-cropping (pip)
* easy-thumbnails (pip)
* django-sortedm2m (pip)
* requests (pip)
* sqlite3 (for development)
* postgres (for production)
* gunicorn (for production)

Optional dependencies
* Virtual Env (for encapsulating the projects dependencies)

## Development
1. Install all dependencies. Pip dependencies can be installed using the requirements.txt file `pip install -r requirements.txt` from the project root, once the repository has been cloned.
2. Create the local database. On development we use sqlite. This can be done using `make migrate` if make is installed. Otherwise one must run `python manage.py makemigrations website && python manage.py migrate`
3. Create a super user using `python manage.py createsuperuser` to manage the admin interface.
4. The scripts `python manage.py importpubs` and `python manage.py importtalks` will import from bibtex and xml files located under the import directory respectively. These files are designed for use by the Makeability lab and will import from Jon Froehlich's website cs.umd.edu/~jonf
5. Add a file googleaccount.py in the website directory. This file should contain only the line ANALYTICS_ACCOUNT = 'your_google_analytics_email'. Replace your_google_analytics_email with whatever google account has been set up to track the domain in google analytics.
6. Follow the instructions [here](https://developers.google.com/analytics/devguides/reporting/core/v3/quickstart/service-py) to install google analytics api using pip, and to download the p12 private key from google analytics for authentication. This file should also go in the website directory along with the googleaccount.py file.
7. Run server using `make run` or `python manage.py runserver` if make is not installed.

