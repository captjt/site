---
layout: post
cover: false
title: Python & VS Code
date:   2017-01-18 05:00:00
tags: technologies
subclass: 'post tag-content tag-technologies'
categories: 'jt'
navigation: True
logo: 'assets/images/logo.png'
---

### Working With VS Code

There are a lot of IDE/Editors out there for Python and I keep getting asked from colleages about how I setup my environment locally. Instead of trying to chain emails, slack/rocketchat each other with long conversations this article will help solve some problems.

VS Code is a fantastic editor that I have been using full-time for about 6 months now (converted from Sublime). I have also used PyCharm and Atom with Python projects and I do think they are viable options as well, but I always end up back with VS Code no matter how hard I try to force myself out of it. I think the flexibility I have with VS Code to develop all types of applications in one place is where I fell in love. I use it for Go, Python, PHP, JavaScript and Scala projects so far.

On to the setup...

### Setup

The biggest thing I try and promote with Python and projects are **virtual environments**. They provide developers the flexibility to have many different Python versions specific to an application. Another promotion I have for Python is **Python 3 -- use it**... don't stray from it. I don't advocate for people to develop anything in **legacy** Python (2.7). Python 2.7 is so slow that Google even compiles their Python 2.7 code to Go to improve performance [grumpy](https://github.com/google/grumpy). Python 3 fixes a ton of the performance problems that come up in 2.7 -- use Python 3!

When you are first setting up your Python project setup your virtualenv with a tool like **pyvenv** ... in your trusty *Terminal*

~~~
# "env" will be the directory that your local dependencies will be install under`
$ pyvenv env
# this will activate your virtualenv`
$ source env/bin/activate
# You should see (env) at the beginning of the line in your terminal session now.
~~~
{: .language-bash}

It's best practice to have all of your dependencies stored in a file named `requirements.txt` at the root of your project's structure. Here is a sample *requirements.txt* file with production dependencies I have for a small flask application. Yours obviously will have different dependencies depending on the Python packages you need for you application.

*requirements.txt*

~~~
# Everything needed in production

wheel

# Flask
Flask
Flask-RESTful
blinker

# Database
Flask-SQLAlchemy
psycopg2
GeoAlchemy2

# Migrations
Flask-Migrate

# Deployment
gunicorn
gevent

# Utils
simplejson
~~~

To install all these dependencies we just do a simple `pip install -r requirements.txt` **BUT** we also need to make sure we have our project's virtual environment activated before we do the installation.

Your dependencies will be install in that **env** directory and we can locate them if we want to just by looking through the `env/lib/python3.x/site-packages` directory. On to the VS Code integration ==>> WOO.

### VS Code

The first thing you will need to install is a extension called [Python](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python). You can install it with the extension panel on the left side navigation (last button on the bottom). And search for Python -- it's the most downloaded extension when you search for Python. You can read through the docs on that extension to do more custom things but here are the main settings you will need to paste in your **Workplace Settings/User Settings** under your **Preferences**.

~~~
// Python specific
"python.pythonPath": "${workspaceRoot}/env/bin/python3.6",
"python.autoComplete.extraPaths": [
    "${workspaceRoot}/env/lib/python3.6/site-packages"
],
"python.devOptions": [
    "DEBUG"
],
"python.linting.pylintEnabled": true,
"python.linting.flake8Enabled": false,
"python.linting.pylintArgs": "pylint --load-plugins pylint-flask",
"python.formatting.provider": "autopep8",
"python.unitTest.unittestEnabled": false,
"python.unitTest.pyTestEnabled": false,
"python.unitTest.nosetestsEnabled": false,
~~~
{: .language-JavaScript}

Paste that into your **settings.json** file that will come up when you select **User Settings** or **Workplace Settings**. The **KEY** to those settings is the "python.pythonPath" and "python.autoComplete.extraPaths" you have to point that to the *Path* you have to your local virtual environment folders. Also! I will recommend you keep project settings specific to your **Workplace Settings** because you might have many different Python versions when going from project to project and keeping the **User Settings** might break the paths.

There are some custom settings like the linting/testings you can do inside of the editor as well. I tend to stick with my terminal to run external things like that but you can play around with it -- this should give you the basic VS Code environment for developing with Python.
