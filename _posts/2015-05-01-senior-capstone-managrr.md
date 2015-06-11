---
layout: post
title: 'Senior Capstone: ManaGRR'
date: '2015-05-02'
author: Kevin L
thumbnail: /images/post_capstone/grr_thumbnail.png
---

Champlain College has its students do a capstone during their senior year. It is a 2 semester long project that involves taking everything learned in the curriculum over the past 3 years.

For Computer Networking and Information Security, it can be a research or development project.
 
I chose to work with GRR Rapid Response for my capstone. I've had experience working with GRR before, because [previous](http://107.170.7.133/blog/?p=95) [students](https://benvirgilio.com/uploads/RAR/RapidAutomatedResponse.pdf) did research projects on it. I understood the purpose of the project and how useful it could be for businesses.


My idea was to create an easy way to bring GRR in to businesses for incident response and live forensics.

And that's how **ManaGRR** started.

It's a management program that will setup a customized version of GRR for each incoming client.  
 Essentially to sell _GRR as a Service_.

Python-Flask is an incredible platform for web applications. It's a microframework that really made this project easier to write. The only troubles I ran into was that I was learning more about the language over time and I had to keep going back and changing things.

Abstract:
>GRR Rapid Response is an application for incident response and live forensics. It involves dealing with separate servers communicating together in a secure manner. Using this application for production use is quite difficult to manage when dealing with multiple clients. The result is a python-flask application that communicates with Proxmox hypervisor to provision each virtual role with a proper private network stack. It expands GRR's capability and ease-of-setup especially when dealing with a growing business.


[Capstone Poster](/images/post_capstone/poster-kevin_law.pdf)  
[Capstone Paper](/images/post_capstone/Capstone_Final_KevinLaw.pdf)   
[Github Project](http://github.com/thatarchguy/Managrr)  

Free & Open Source.
