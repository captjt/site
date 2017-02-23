---
layout: post
cover: false
title: Elasticsearch Info/Resources
date:   2016-12-13 05:00:00
tags: technologies
subclass: 'post tag-content tag-technologies'
categories: 'jt'
navigation: True
logo: 'assets/images/ghost.png'
---

Elasticsearch is a "NRT" (Near Real Time) search platform which means the data that is being ingested is nearly searchable within < 1 second.

## Basic Concepts

You can learn more about Elasticsearch and it's basic concepts at their [docs](https://www.elastic.co/guide/en/elasticsearch/reference/current/_basic_concepts.html)

##### Cluster
The cluster concept in ES is that you can have a bunch of nodes (or servers) that hold all your data together.

##### Node
A node is a single elasticsearch server (or service) that is running holding a collection of indexes and data regarding each index.

##### Index
An index is thought of as a "database" in relational terms. It is a higher level concept that can be thought of as similar characteristics for the document data.

##### Type
Types can be thought of as "tables" in relational terms. It is a more definied catagorization of data for each document stored in ES.
One way to think about it is you have a higher level index of "User Data" and you can have "Order Data", "Comment Data", etc.

##### Document
These are the lowest level catagorization of document data in ES. Think of these as "rows" in a relational mindset.  You can have attributes in each document that describe the data's features.

##### Sharding/Replication
We can do multiple sharding instances on indexes if we are storing huge amount of document data in them. [Sharding](https://www.elastic.co/guide/en/elasticsearch/reference/current/_basic_concepts.html#_shards_amp_replicas)

## Resources

### Elasticsearch
- [Quick Reference](https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html)

### Elasticseach (language specific)
- [elasticsearch-dsl](http://elasticsearch-dsl.readthedocs.io/en/latest/)
- [elasticsearch-py](http://elasticsearch-py.readthedocs.io/en/master/)
- [elasticsearch](https://www.elastic.co/products/elasticsearch)
- [elastic](https://olivere.github.io/elastic/)
- [more elastic](https://godoc.org/gopkg.in/olivere/elastic.v3)

### External Backend Frameworks
- [flask](http://flask.pocoo.org/)
- [flask-restful](http://flask-restful-cn.readthedocs.io/en/0.3.5/)
- [gin](https://gin-gonic.github.io/gin/)

### Small Examples
- [rest-api](https://github.com/jtaylor32/elasticsearch-restful-api)
- [bulk-ingestion](https://github.com/jtaylor32/elasticsearch-bulk-ingestion)

### Web Scrapers
- [nutch](http://nutch.apache.org/)
- [scrapy](https://scrapy.org/)
- [scrape](https://github.com/yhat/scrape)

### Blogs/Books
- [python-scraping](http://docs.python-guide.org/en/latest/scenarios/scrape/)
- [more-python-scraping](http://www.gregreda.com/2013/03/03/web-scraping-101-with-python/)
- [go-scraping](https://schier.co/blog/2015/04/26/a-simple-web-scraper-in-go.html)
- [Web Scraping with Python](http://shop.oreilly.com/product/0636920034391.do)
- [Scraping with Nutch](https://qbox.io/blog/scraping-the-web-with-nutch-for-elasticsearch)

### Other ES Articles
- [es-in-go](http://goinbigdata.com/working-with-elasticsearch-in-go/)
- [testing-go-es](http://126kr.com/article/5c4jgpiwtwp)
- [python-with-es](https://msftstack.wordpress.com/2016/02/18/making-a-book-search-engine-in-python-and-elasticsearch/)
- more to come...



continued...
