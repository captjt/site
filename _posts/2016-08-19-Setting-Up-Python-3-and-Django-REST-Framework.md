---
layout: post
cover: false
title: Setting Up Python 3 and Django REST Framework
date:   2016-08-19 05:00:00
tags: technologies
subclass: 'post tag-content tag-technologies'
categories: 'jt'
navigation: True
logo: 'assets/images/logo.png'
---

## Objective:
We want to setup an Python 3 environment on our machine to start developing in Python 3! Once we set up Python and all the packages necessary for Django development -- we want to set up our first Django Framework REST Application.

### Install Xcode and Homebrew :

#### XCode

We first need to install XCode or using Homebrew to install Python3 will throw an error.

First, we must go into whatever terminal application that we use and paste this line into the command:

~~~
xcode-select --install
~~~
{: .language-bash}

#### Homebrew

Homebrew is a package manager for OS X -- this is a very powerful tool to install, update, list and many more options for packages that we will use as developers. "Homebrew installs all the things Apple didn't get."

~~~
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
~~~
{: .language-bash}

To make sure our brew installations will be used over some of the OS X stock binaries we need as a line in our .bash_profile file. Open or create the file ~/.bash_profile and paste this line into it.

~~~
export PATH=/usr/local/bin:$PATH
~~~
{: .language-bash}

### Install Python 3 :

Onto Python installation...

Once you have Homebrew installed and ready to be used we can do a simple command line call like this to install Python3

~~~
brew install python3
~~~
{: .language-bash}

...and voilà! Do a simple

~~~
python3 --version
~~~
{: .language-bash}

and you should have the most recent version of Python3. This comes with a bunch of libraries -- *pip3* (a package manager), *Setuptools* (a project packaging tool), and *pyvenv* (a virtual environment tool). These three tools are very powerful and there are others out there that can replace these. Although, with that being said -- I would recommend starting with these three for starting out.

Note: I personally use *virtualenv* which is another virtual environment tool like *pyvenv*.

### Creating Our Django-Rest-Framework Project :

After we setup our Python3 and validate our verion is up to date - we will start our Django-Rest-Framework project. So to start out we must create a virtual environment to work out of so we must make a directory and inside of that empty directory we will do a :

~~~
virtualenv env
~~~
{: .language-bash}

..this will install our environment to a specific version of Python we want to it to be in. You can checkout the docs for *virtualenv* to see more options. After we do the environment installs we will create out our project. So we need to install django, djangorestframework, pgycopg2 (for PostgreSQL). You can interchange any database packages based on whatever backend you want the ORM to be talking to. To do these installs we must use *pip*.

First activate your 'env' by :

~~~
source env/bin/activate
~~~
{: .language-bash}

Then do a :

~~~
pip install django djangorestframework psycopg2
~~~
{: .language-bash}

After that we need to start out project so we do a :

~~~
django-admin.py startproject project_name
~~~
{: .language-bash}

Based on these instructions you will have Xcode, Homebrew, Python3, and a python environment with the start of a fantastic RESTful framework. Stay tuned for more posts about Django-Rest-Framework tricks and tutorials.








