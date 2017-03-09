---
layout: post
cover: 'assets/images/future-technologies.jpg'
title: Introduction on Migrating to Microservices
date:   2017-02-14 05:00:00
tags: technologies
subclass: 'post tag-content tag-technologies'
categories: 'jt'
navigation: True
logo: 'assets/images/logo.png'
---

## Why Microservices?

This is an obvious question raised when applications or companies move to a microservices approach. There is a massive use case for the migration to microservices. The perfect time is you are at a scale or a place in your application's life cycle where you cannot iterate fast enough on top of your monolithic application. There are so many more resources out there on when or why to switch to this architecture it's not really worth "beating a dead horse" on the subject.

#### Technologies to Migrate With?

You might be wondering where to start and what approach to take -- if you have the luxury to migrate and refactor everything without a time constraint you're lucky! Majority of us don't have that luxury and really don't have the full buy in from our management or superiors to even "fix" our already working application. Invest in your products and invest in the future of your products is all I can say. This means containers and efficiency. Containers are here.. and here to stay. When I say that majority of companies or teams are just dabbling in containers and what to do with Docker or Rkt ("Rocket" -- a CoreOS product) it's understandable but **invest** in modern technologies. Invest in yourselves and your application's future start getting up to speed with containers it is so simple to spin up applications with these to proof them for teams/management. The ease of use to manage and bundle up applications is a major selling point! 

#### After Approval

Uh-oh... Now what right? You need a team to fully focus and sell out on these technologies. Regardless of the learning curve you will probably face when moving to containers and orchestration tools you will make it through it! The best things in life aren't always the easiest things to come by -- but a team focused on the future of the application and in turn the company's future success. 

![team](./../assets/images/small-team.jpg){:class="img-responsive"}

If you are a small team pick a uniformed language to start with that everyone is comfortable developing in. A key feature you will need to take into account is how easy is it to run on Linux. I am an advocate for Go but also love NodeJS and Python. Do what's best for you team and make sure you know that microservices don't lock you into one single language it helps at an initial migration having a single or a couple uniform languages for developers to agree on.  

#### Container Orchestration

The biggest decision you will make after the initial migration movement is how in the world will you manage all of these containerized services. Invest in some time in researching which tool or tools fit your application's use cases. Here are a list of some of the major self-hosted orchestration tools -- if you want something like an already hosted service of these things Google, Mesosphere, Rancher and others support enterprise platforms for these kinds of things already.

Self-Hosted

- [Kubernetes](https://kubernetes.io/)
    - Came out of Google -- built based on Google's "Borg" project
    - Backed and supported by majority of the leading tech companies like 
- [DC/OS](https://dcos.io/)
    - Product built around Apache Mesos
    - Blog [post](https://mesosphere.com/blog/2016/04/19/open-source-dcos/) on the release of DC/OS
    - Backed and supported by companies like Microsoft, Accenture, and others
- [Docker Swarm](https://docs.docker.com/engine/swarm/)
    - Not as widely used in the community

There might be other options out there but a big thing to note when migrating to a microservice architecture is the communities. Kubernetes has been the biggest player in this space and is backed by **A LOT** of companies using these technologies. Many companies using Kubernetes support the tool by giving back to the open sourced code as well.

#### Service Interactions

More architecture decisions you will have to make are how your services will interact and managing an API gateway into your services. There are major complexities that come with microservices interacting with each other and how to monitor what is going on -- but there are tools out there to solve this. Later on I will talk about tooling decisions you will have to make when managing service discovery, tracing, monitoring, transporting, circuit breakers (retries) and other things. 

Don't feel overwhelmed when making the leap of migrating architectures it's an **investment**.