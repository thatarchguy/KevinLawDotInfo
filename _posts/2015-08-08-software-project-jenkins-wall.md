---
layout: post
title: 'Project: Jenkins CI HUD'
date: '2015-08-08'
author: Kevin L
thumbnail: '/images/post_jenkins_hud/thumbnail.png'
---
[![License](http://img.shields.io/:license-mit-blue.svg)](http://jenkins-ci-hud.mit-license.org)
![Python](https://img.shields.io/badge/python-2.7-blue.svg)
![Flask](http://flask.pocoo.org/static/badges/made-with-flask-s.png)

There are other dashboards out there but none that I found met my needs.

A dashboard that integrates with your workflow is key. I really like the pilot+master feature branch workflow. It's essentially the [gitlab flow](https://about.gitlab.com/2014/09/29/gitlab-flow/) with different naming schemes. 

>Developers feature branch off pilot. Pilot gets deployed to pilot servers, and master is your production release build. A merge into master is tagged and released. 

I may blog about this at another time. It flows really well with chatops ;).

So every feature branch is a merge request that gets tested in Jenkins! **This is where other dashboards fail**.
They usually just have a big box that's <span style="color:red">red</span> or <span style="color:green">green</span> to indicate the general job failed or passed.

I wanted a list of the latest tests per run and big boxes for the deploys. That is the relevant information to me. Looking up at the display to see my current build failed, or a flashing <span style="color:blue">blue box</span> telling me JohnDoe is deploying to pilot is what I want to see.

This application requires parameterized builds in the job config. If you are using the gitlab-ci plugin in Jenkins to link into your gitlab server, then you are set.  
You'll also need the Jenkins notifier plugin enabled. This enables Jenkins to do a post request to a webhook.  
I have more instructions in the [readme](https://github.com/thatarchguy/jenkins-ci-hud).

I'm not a designer. And it's crude. But it _works_. Feel free to pull request if you want to make it look pretty.

More can be found at [Jenkins CI HUD project page](https://github.com/thatarchguy/jenkins-ci-hud)

![Hud](/images/post_jenkins_hud/hud.png)
