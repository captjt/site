---
layout: post
cover: false
title: Creating A RESTful API w/ Elasticsearch
date:   2016-12-20 05:00:00
tags: technologies
subclass: 'post tag-content tag-technologies'
categories: 'jt'
navigation: True
logo: 'assets/images/ghost.png'
---

#### (In Progress)

### Objective:
We want to establish a way to query _**Elasticsearch**_ through a RESTful API's endpoints.

### Pre-Requirements

- Python 3+
	- Knowledge of virtual environments, dependency management with **PIP**
- Elasticsearch 2.X
	- Install Elasticseach on Centos 7 --> [es-on-centos-7](https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-elasticsearch-on-centos-7)
    - [Elasticsearch](https://www.elastic.co/products/elasticsearch)


### WSGI Application (Flask)

We are going to be using the micro-framework, *flask*, in this tutorial.  We will use the *elasticsearch-dsl-py* package that elastic created to interact with elasticsearch at a more high level abstraction.

#### Project Structure

~~~
.
├── app/
│   ├── index.md
│   ├── api/
│   │   ├── __init__.py
│   │   ├── resources/
│   │   │   ├── __init__.py
│   │   │   ├── search.py
│   │   ├── search_config/
│   │   │   ├── __init__.py
│   ├── __init__.py
│   ├── config.py
│   ├── extensions.py
│   ├── helpers.py
├── env/
├── requirements/
│   ├── development.txt
│   ├── production.txt
├── tests/
│   ├── unit/
│   │   ├── test_*.py
│   ├── __init__.py
│   ├── conftest.py
├── manage.py
├── requirements.txt
├── runtime.txt
├── setup.cfg
~~~

To be continued...
