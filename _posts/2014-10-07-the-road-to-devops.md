---
layout: post
title: The Road to DevOps
date: '2014-10-07T20:37:00.001-07:00'
author: Kevin L
thumbnail: /images/post_roaddevops/thumb_2014-10-07-211605_667x198_scrot.png
---

<h2 style="text-align: center;">Development practices are just as important as the code itself</h2>
<div style="text-align: center;"><span style="font-size: x-small;">This isn't some big advertisement for these products, but I totally endorse and use these for all my projects.</span>
</div>

[What is this DevOps thing anyway?](http://www.jedi.be/blog/2010/02/12/what-is-this-devops-thing-anyway/)  

I always thought I was a good programmer. Changes to my code always consisted of making a change, hitting save, then compiling. And that's it. If a change was made that ruined everything, I could not rollback.

That was bad.  

This is my journey.  


<table cellpadding="0" cellspacing="0" class="tr-caption-container" style="float: right; text-align: right;">
    <tbody>
        <tr>
            <td style="text-align: center;">
                <a href="/images/post_roaddevops/2014-10-07-211605_667x198_scrot.png" imageanchor="1" style="margin-left: auto; margin-right: auto;"><img border="0" src="/images/post_roaddevops/2014-10-07-211605_667x198_scrot.png" height="117" width="400" />
                </a>
            </td>
        </tr>
        <tr>
            <td class="tr-caption" style="text-align: center;">We've all been here.</td>
        </tr>
    </tbody>
</table>


Version control opened doors for me. It allowed me to stop worrying about saving edits.

I could finally code without worry. I no longer had copies of copies of files in a folder _just in case_.

<a href="https://github.com/" imageanchor="1" style="clear: left; float: left; margin-bottom: 1em; margin-right: 1em;"><img border="0" height="165" src="https://assets-cdn.github.com/images/modules/logos_page/Octocat.png" width="200" /></a>

Branches for days and [Git-flow ][3]as my train tracks.

[Github][4] is awesome for git. It is a effective platform for healthy and productive collaboration.

Commits can be diff'd in a simple interface, repositories can be forked and merged with ease, and it makes managing a project that much easier. Most of my projects are hosted on Github now.

Issues and milestones integrate very well with repositories.

![][5]I discovered [waffle.io][6], a startup dedicated to created kanban-style boards for Github issues.


<a href="https://waffle.io/" imageanchor="1" style="clear: right; float: right; margin-bottom: 1em; margin-left: 1em;"><img border="0" height="" src="https://avatars3.githubusercontent.com/u/4751584?v=3&s=400" width="144"/></a>

It was designed to work with Github issues and help visualize them in a progress board fashion.

This is perfect for large projects where keeping track of who is doing what is vital.

I've even heard of companies using github issues as their ticketing system for helpdesk.

![][7]

Continous integration takes code that developers commit and builds it with the mainline.   
This process can detect bugs and issues without user intervention. It prevents merging in broken code.

Once it is set up (and setup is extremely easy) it can be an integral part of development.  



[Travis-ci][8] is a popular continuous integration tool that can seamlessly integrate with Github projects.

All pull-requests can be automatically tested against the mainline to test if it is safe to merge.

Virtualization is something I've always done. I'd make various virtual machines to do various tasks. I never thought about it in development until I realized that my friends ran windows and I ran linux. I'd say, "Well just run a virtual machine with apache, php, and mysql". It was always a hassle, and never really got anywhere. They would install different packages like nginx or an old version of php.

&nbsp;
<a href="https://www.digitalocean.com/assets/images/logos-badges/png/DO_Logo_Vertical_Blue-75e0d68b.png" imageanchor="1" style="clear: left; float: left; margin-bottom: 1em; margin-right: 1em;"><img border="0" height="126" src="https://www.digitalocean.com/assets/images/logos-badges/png/DO_Logo_Vertical_Blue-75e0d68b.png" width="200" /></a>

I then found out about Virtual Private Servers (VPS) and [Digital Ocean][10]. A VPS grants to ability to run a virtual server with popular operating systems and&nbsp; a public IP.

Their API allows for creating these VPS's with a few commands. It's very similar to Amazon AWS EC2 instances, but way less to learn.

They cost money though. But it does allow for a quick and easy way to bring up a public box to show code or develop on.

<a href="http://upload.wikimedia.org/wikipedia/commons/8/87/Vagrant.png" imageanchor="1" style="clear: right; float: right; margin-bottom: 1em; margin-left: 1em;"><img border="0" src="http://upload.wikimedia.org/wikipedia/commons/8/87/Vagrant.png" height="200" width="163" /></a>

That's when I learned about [Vagrant][12].  

This awesome tool let's you deploy a virtual machine, install software, and run commands all with a config file and 'vagrant up'.

Developers can just clone your code and type 'vagrant up', and have a virtual machine complete with the exact software as everyone else working on the project. It is designed to be portable and reproducible.

Vagrant is awesome. It has plugins to deploy onto Amazon EC2 instances, Digital Ocean, and various other virtualization platforms.

It creates an operating system independent application, defeating the age old "Works on machine" argument.

<div class="separator" style="clear: both; text-align: center;">
    <a href="http://ddf912383141a8d7bbe4-e053e711fc85de3290f121ef0f0e3a1f.r87.cf1.rackcdn.com/docker-whale.png" imageanchor="1" style="clear: left; float: left; margin-bottom: 1em; margin-right: 1em;"><img border="0" src="http://ddf912383141a8d7bbe4-e053e711fc85de3290f121ef0f0e3a1f.r87.cf1.rackcdn.com/docker-whale.png" height="200" width="200" />
    </a>
</div>

[Docker][14] creates a quick and easy virtual environment utilizing linux containers. A file much like a vagrantfile is created that has all the specifications and commands for the VM to run on install.

It is very fast to build your application into a container. It even allows to diff entire containers for changes. These containers can be sent to other members of the team for testing and QA.

Docker is being used in production to deploy applications more and more now. With operating systems like CoreOS, creating a fleet of deployments is simple.

<a href="/images/post_roaddevops/photo.jpg" imageanchor="1" style="clear: right; float: right; margin-bottom: 1em; margin-left: 1em;"><img border="0" src="/images/post_roaddevops/photo.jpg" height="199" width="200" />
</a>

Having the same configurations across a distributed environment running your application would be a difficult job without tools like [Puppet][16].

This tool audits each node, and enforces changes based on templates.

Going from development containers to production can utilize tools like Puppet to get the job done.

DevOps is more than just tools. It's also a mindset. Developers working with Sysadmins to create a environment that's conductive to creating the product. Everyone is on the same team, so why not use that to your advantage?

Companies like [Etsy][17] and [Spotify][18] have created processes and tools that are almost completely automated and will not push broken code to production. It makes going from development to production as painless as possible. New hires can push to production on their first day.

All these tools have way more features than I listed. I just gave an oversimplified overview of how they contribute to the process.

[Github - Fimiwal][19] is a project that I started that is utilizing each of these tools and strategies that I mentioned here. It only took a few extra steps to setup, and working on the project is extremely easy because of it. Less time is spent working on the environment, and more time is spent on the code.

[1]: http://www.kevinlaw.info/images/post_roaddevops/2014-10-07-211605_667x198_scrot.png
[2]: https://assets-cdn.github.com/images/modules/logos_page/Octocat.png
[3]: http://nvie.com/posts/a-successful-git-branching-model/
[4]: http://github.com/
[5]: https://waffle.io/blog/assets/images/site-logo.png
[6]: http://waffle.io/
[7]: https://education.travis-ci.com/img/travis-mascot-200px.png
[8]: ttps://travis-ci.org
[9]: https://www.digitalocean.com/assets/images/logos-badges/png/DO_Logo_Vertical_Blue-75e0d68b.png
[10]: https://www.digitalocean.com/
[11]: http://upload.wikimedia.org/wikipedia/commons/8/87/Vagrant.png
[12]: https://www.vagrantup.com/
[13]: http://ddf912383141a8d7bbe4-e053e711fc85de3290f121ef0f0e3a1f.r87.cf1.rackcdn.com/docker-whale.png
[14]: https://www.docker.com/
[15]: http://www.kevinlaw.info/images/post_roaddevops/photo.jpg
[16]: http://puppetlabs.com/
[17]: http://codeascraft.com/2010/05/20/quantum-of-deployment/
[18]: https://github.com/spotify/helios
[19]: https://github.com/thatarchguy/Fimiwal
