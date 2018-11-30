---
layout: post
title: 'Distributing a Flask App as a Package'
date: '2016-04-15'
author: Kevin L
thumbnail: '/images/post_distribute_flask/logo.png'
tags:
- development
- python
- flask
- devops
categories:
- Development
- Devops
---

I've deployed many Flask applications to production servers. My current method is pretty awesome ([Packer](https://www.packer.io/) + [Ansible](https://www.ansible.com/) + [Terraform](https://www.terraform.io/)).  
But how do you distribute Flask applications for users to run?

 Recently, I helped write a Flask application called [Postmaster](https://github.com/StackFocus/PostMaster) for [StackFocus](https://www.stackfocus.org). Since most web applications are full stack these days, they involve a lot of different moving parts.

 We ran into the problem of having _20+ steps_ to install our application. That is no bueno. We made a Dockerfile for users to pull, but we didn't want to force our users to use Docker.

Some research led me to [Chef-Omnibus](https://github.com/chef/omnibus). Gitlab uses it to install their software. It takes a chef recipe and turns it into a deb, rpm, dmg, or msi package for various different operating systems.

I don't use Chef. [I've taken preference to Ansible]({% post_url 2015-10-21-why-ansible-is-awesome %}) for provisioning my applications, simply because it's very easy and mostly everything is a core module. Ansible _does not_ have a cool Omnibus tool to create packages for playbooks. So...I improvised using [FPM](https://github.com/jordansissel/fpm)!


[FPM](https://github.com/jordansissel/fpm) is a trivial way to create packages. Their tagline says it all:
>FUNDAMENTAL PRINCIPLE: IF FPM IS NOT HELPING YOU MAKE PACKAGES EASILY, THEN THERE IS A BUG IN FPM.

If you haven't already, upgrade your application's install script to an Ansible playbook! It's really easy and you will kick yourself for not doing it from the start.


## Create a Debian package for an Ansible Playbook
Requirements:

 - - Working Ansible playbook to deploy application to a server.
 - - `gem install fpm`

Just modify this script as needed, and save as `build_release.sh` in your repository

```bash
#!/bin/bash
# build_release.sh
REVISION=`git describe --abbrev=0 --tags | cut -c 2-`
fpm -s dir -t deb -n "postmaster" -v $REVISION --prefix /opt/postmaster/git \
 --description 'PostMaster is a beautiful web application to manage domains, users, and aliases on a Linux mail server'  \
 --url 'https://github.com/StackFocus/PostMaster'  \
 --after-install ops/ansible/run_ansible.sh -d git \
  -d python -d python-pip -d python-dev -d python-virtualenv \
  -d libldap2-dev -d libssl-dev -d libsasl2-dev -d libffi-dev \
  -d apache2 -d libapache2-mod-wsgi -d libmysqlclient-dev ./
```

Looks easy right? Definitely read [FPM's documentation](https://github.com/jordansissel/fpm/wiki) if you have any problems.

Beware - You cannot use `apt:` or `pkg:` statements in your playbook since you are installing this using your package manager, which creates a lock. Instead of shuffling the lock around, just mark the packages as dependencies and have your package manager take care of it! (It's almost like that's what they were made for!)

I use Vagrant for development, so I have all the packages in my playbook marked `when: provision_type == "dev"`

The trick here is the `--after-install` script, which is just a bash script to call the ansible playbook. You can get fancy and specify other options, like `--after-upgrade`.

Save this script in the directory with your playbook so it will run when the packge is installed.

```bash
#!/bin/bash
# ops/run_ansible.sh
# --after-install script
ansible-playbook -i "localhost," -c local --extra-vars \
"remote_user=ubuntu" /opt/postmaster/git/ops/ansible/site.yml
```


## Extra Curricular
### Travis-CI build packages and push to Bintray

![pipeline](/images/post_distribute_flask/pipeline.png)

[Bintray](https://bintray.com) is awesome because it will setup apt repos for your users to add to their sources.list. You can also host Vagrant boxes, Docker images, and more!

 - - Add your respository to Travis-CI
 -  - echo your bintray api key to `deploy.key`  
 -  - `travis encrypt BINTRAY-API-KEY --add deploy.key && rm deploy.key`


```
# .travis-ci.yml

before_deploy:
  - gem install fpm
  - ./build_release.sh

deploy:
  provider: bintray
  file: .bintray_descriptor.json
  user: thatarchguy
  key:
    secure: [generated from travis encrypt]
  dry-run: true
  on:
    tags: true
```

```
# bintray_descriptor.json
{
    "package": {
        "name": "PostMaster",
        "repo": "PostMaster",
        "subject": "stackfocus",
        "desc": "PostMaster is a beautiful web application to manage domains, users, and aliases on a Linux mail server ",
        "website_url": "https://github.com/StackFocus/PostMaster",
        "issue_tracker_url": "https://github.com/StackFocus/PostMaster/issues",
        "vcs_url": "https://github.com/StackFocus/PostMaster.git",
        "licenses": ["AGPL"],
        "labels": ["Postmaster", "Stackfocus", "Flask"],
        "public_download_numbers": true,
        "public_stats": true
    },
    "version": {
        "name": "1.0.0",
        "desc": "Abbey Road"
    },
    "files":
        [
        {"includePattern": "*.deb" }
        ],
    "publish": true
}
```



There you have it! You will have a working .deb package for users to install your flask web application. You can use this method for other types of projects as well!

Check out our project [PostMaster](https://github.com/StackFocus/PostMaster) to see these examples in a working scenario.

Note: I'll probably update this blog post as I go.
History can be found on [github](https://github.com/thatarchguy/KevinLawDotInfo/commits/gh-pages/_posts/2016-04-15-distributing-flask-as-a-package.md) or [gitlab](https://gitlab.com/thatarchguy/KevinLawDotInfo/blob/gh-pages/_posts/2016-04-15-distributing-flask-as-a-package.md)
