---
layout: post
cover: 'assets/images/lighthouse.jpg'
title: Elasticsearch At A Glance
date:   2017-02-14 05:00:00
tags: technologies
subclass: 'post tag-content tag-technologies'
categories: 'jt'
navigation: True
logo: 'assets/images/logo.png'
---

## Why Elasticsearch?

Elasticsearch has some major upsides to it when using it on as a developer.  It takes almost four minutes to stand up on a CentOS server and even quicker on my development machine (Mac OSX with Homebrew). Regardless whether or not you are using it in those environments it is still very quick and easy to do an install with configurations. Configurations are made EASY. Elasticsearch will, based on your settings, do automatic cluster replication on indexes you create. You can tell it that you want five, ten, three, whatever of how many replication shards you want it to make. This will increase the productivity you have along with performance and fault tolerance of the storage layer. You will not need to worry about diving in and setting up major in-depth configurations when it really is meant to be abstracted out for you - that helps developers work on other aspects of the applications running on Elasticsearch. Having the fault tolerance of having many shards replicated to however many nodes you have inside of your cluster per index is huge. This might seem scary at first when it does this for you out of the box, but when you dive into the real stuff and see what it’s doing - it’s quite nice.

JSON everything. Web development and applications have been revolutionized by RESTful and JSON type of manipulation of data. Majority of people either know REST of have used it one way or another. Elasticsearch is catering to the modern technologies. Because JSON is such a huge and easy way to store data many developers love interacting with it. All of the queries, responses, mappings, anything is done with Elasticsearch is through JSON! (HUGE simplicity for people to hit the ground running)

I cannot think of anything that has been difficult learning how to interact or use Elasticsearch. The community is massive around it and honestly cannot see anything wrong with moving towards it.

Here are some excerpts from elastic.co (official company behind ES) and others regarding the technology…

“Elasticsearch provides the ability to subdivide your index into multiple pieces called shards. When you create an index, you can simply define the number of shards that you want. Each shard is in itself a fully-functional and independent "index" that can be hosted on any node in the cluster.”  -- Elasticsearch official docs

### Sharding concepts …

It allows you to horizontally split/scale your content volume
It allows you to distribute and parallelize operations across shards (potentially on multiple nodes) thus increasing performance/throughput

### Replication concepts …

It provides high availability in case a shard/node fails. For this reason, it is important to note that a replica shard is never allocated on the same node as the original/primary shard that it was copied from.
It allows you to scale out your search volume/throughput since searches can be executed on all replicas in parallel.

### Use Cases -- Real Companies Using ES!

Ebay -- [this-is-not-small-data](https://www.elastic.co/videos/ebay-and-elasticsearch-this-is-not-small-data)

Goldman Sachs -- [stack-changed-goldman-sachs](https://www.elastic.co/elasticon/conf/2016/sf/how-the-elastic-stack-changed-goldman-sachs)

Netflix -- [message-analytics-at-netflix](https://www.elastic.co/elasticon/conf/2016/sf/dude-where-are-my-messages-message-analytics-at-netflix)

Facebook -- [hackathon-to-production-facebook](https://www.elastic.co/elasticon/2015/sf/from-hackathon-to-production-elasticsearch-facebook)

Many more [here](https://www.elastic.co/use-cases) and what has driven companies to Elasticsearch (many of which transitioned from Solr)
