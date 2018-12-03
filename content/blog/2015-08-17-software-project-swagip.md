---
layout: post
title: 'Project: Docker Scaling & SwagIP'
date: '2015-08-17'
author: Kevin L
thumbnail: '/images/post_swagip/thumbnail.png'
tags:
- development
- python
- flask
categories:
- Development
---
[![License](http://img.shields.io/:license-mit-blue.svg)](http://swagip.mit-license.org)
![Python](https://img.shields.io/badge/python-2.7-blue.svg)
![Flask](http://flask.pocoo.org/static/badges/made-with-flask-s.png)

My friend [Matt Prahl](https://www.linkedin.com/in/matthewprahl) and I decided to work together on some flask stuff. We chose to rewrite http://ifconfig.co in flask.  
Matt did an excellent job of ramping up on flask, since he is a PHP guy at heart (we forgive him).  

This project looked like an excellent way for me to practice scaling docker applications. So I took the opportunity to do so.

The app itself is very useful. If you are at the commandline and you just need your ip address, you can simply curl/wget/fetch/invoke-RestMethod(Powershell) the application.   
Any headers sent are available to be requested as well, such as user-agent and x-forwarded-for. All headers can be pulled with /all.   
The WebUI has all the details.


[Docker-compose](https://docs.docker.com/compose/) is a tool for container linking and deployment. It is great for multi-container applications. It has this nifty little feature called "scale". This feature lets you spin up more containers of your application.

Scaling with docker-compose is easy. You just specify which node you want to scale and the quantity!
It's best to throw a load balancer in front of your application.  

![docker-compose](/images/post_swagip/docker-compose.png)  

Here is my docker-compose.yml  

``` bash
haproxy:
    image: tutum/haproxy
    links:
        - web
    ports:
        - 80:80
web:
    build: .
    expose:
        - 8080
```

Just a little docker compose magic:
```
$ docker-compose up
```

Simple! And with that, I can scale the application up to hundreds of containers.  
![scale](/images/post_swagip/scale.png)  


![loadbalance](/images/post_swagip/loadbalance.gif)  
I made some minor modifications to the app to provide the container id.  
(hint: it's in $HOSTNAME)

Troubleshooting:  
I was having issues with getting the haproxy to see the other nodes. It's best to spin the web containers up before the haproxy:

``` bash
$ docker-compose scale haproxy=0
$ docker-compose scale web=10
$ docker-compose scale haproxy=1
```

More can be found at [SwagIP project page](https://github.com/stackfocus/swagip)
