---
layout: post
title: 'Project: Fimiwal'
date: '2015-08-08'
author: Kevin L
thumbnail: '/images/post-generic.png'
---
[![License](http://img.shields.io/:license-mit-blue.svg)](http://fimiwal.mit-license.org)
![Python](https://img.shields.io/badge/python-2.7-blue.svg)
![Flask](http://flask.pocoo.org/static/badges/made-with-flask-s.png)

This post is a bit late, since I was tasked with writing this software as an assignment in college.
> Develop a solution that uses GIT to create a content file-integrity solution on Windows and Linux. Set it up so that the repository can be stored on a local hard drive or Google Drive.
> The solution should be able to start a scan on a specific host on group of hosts on-demand or an automatic basis specified by the user.

File content integrity is important and often overlooked. If unauthorized changes occur on a system, it is vital to know what changes were made. File content integrity is a major part of information assurance, which is essential in any company.

OSSEC is a product that can do file integrity checking, but for this project we will be using git to handle all the dirty work. A company may not want to use a feature-filled program such as OSSEC and want a slimmer solution.

Fimiwal (_file integrity monitoring in windows and linux_) was designed to meet those needs.  
It's a secure application that can be ran internally or externally on your companies network. For security reasons this should only be an internal application, since it uses ssh and winexe to login to the machines.

It requires some more setup than I like in an application, I apologize for that. I did not want to write my own git server & management solution when gitolite met the needs just fine. 


- Use cases:   
 * Setup monitoring for /etc on all linux servers in your company.
 * Setup monitoring for config file directory on all windows servers.
 * Possible honeypot use?


More (including screenshots!) can be found at [Fimiwal project page](https://github.com/thatarchguy/fimiwal)  

