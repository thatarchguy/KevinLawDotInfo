---
layout: post
title: 'Why Ansible is Awesome'
date: '2015-10-21'
author: Kevin L
thumbnail: '/images/post_ansible/ansible.png'
---

### * Quick to understand and start using (20-80)

### * It's _basically_ fabric with yaml

### * Things like AWS and Docker configurations are core modules

### * Playbooks make sense


Here's a simple playbook that does some powerful stuff with docker.
It will deploy a docker container to a server, and reload if it's changed.

```yaml
---
- name : "My App container"
  docker:
    name: "someapp"
    image: "klaw/someapp"
    registry: "myregistry.lan"
    insecure_registry: "no"
    state: reloaded
    pull: always
    ports:
    - "5000:80"
```
That's it!

Let's take it a step further! We can template it so it's more generic. That way
we can do just edit a variables file to fill in the values for us.  
Make it a Jenkins job with some hooks and you have yourself a way to deploy any application on push.
{% raw  %}
```yaml
---
- name : "{{ app_name | mandatory }} container"
  docker:
    name: "{{ app_name }}"
    image: "{{ repo }}{{ app_name }}"
    registry: "{{ registry_url }}"
    insecure_registry: "{{ insecure_mode }}"
    state: reloaded
    pull: always
    ports:
    - "5000:{{ app_port | mandatory }}"
```
{% endraw %}
You can read more about Docker with Ansible in the [official docs](http://docs.ansible.com/ansible/docker_module.html)


You can do more, like quickly patch all your systems for something like shellshock

```yaml
# Taken from  http://www.atlashealth.com/blog/2014/09/shellshock-vulnerability-patch-with-ansible/

- name: Update bash (Debian)
  apt: pkg=bash state=latest update_cache=yes
  when: ansible_os_family == 'Debian'

- name: Update bash (RedHat)
  yum: name=bash state=latest
  when: ansible_os_family == 'RedHat'

- name: Re-test for vulnerability
  shell: "env x='() { :;}; echo vulnerable' bash -c 'echo test'"
  register: final_test
  failed_when: "'vulnerable' in final_test.stdout_lines"
```

All in all, Ansible is awesome. I like it way more than the other similar tools out there.
>I've never used chef, but in your first 15 minutes you'll be able to do "something" in ansible, meanwhile you'll still be reading about what you can do in chef. And how. [Reddit comment](https://www.reddit.com/r/ansible/comments/3mj2pt/simple_question_what_can_ansible_do_that_chef_cant/cvfej92)
