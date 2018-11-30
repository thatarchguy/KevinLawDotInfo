---
title: 'Gitlab Pipelines Tricks'
date: '2017-10-02'
author: Kevin L
thumbnail: '/images/post_gitlab_pipelines/gitlab-logo.png'
tags:
- devops
- gitlab
- cicd
categories:
- Devops
---

<img src="/images/post_gitlab_pipelines/modern_pipeline.png" style="width: 700px;">  
_Gitlab Pipelines takes CI to the next level_

[Gitlab Pipelines](https://docs.gitlab.com/ce/ci/pipelines.html) allows you to define separate jobs and stages for your CI. No longer are we subjected to a single job that attempts to handle it all.

With this new functionality, we can create complex pipelines that change behavior based on many different factors, including the branch and commit message.

After using Gitlab Pipelines on a team for the better part of a year, here are some cool things I found that you may not know could be done.

## Reuse a Docker Container for each stage

```yaml
variables:
  IMAGE_TAG: $CI_REGISTRY_IMAGE:$CI_COMMIT_REF_NAME

build:
  stage: build
  image: docker:latest
  services:
    - docker:dind
  tags:
    - docker_priv
  before_script:
    - docker login -u gitlab-ci-token -p $CI_JOB_TOKEN $CI_REGISTRY
  script:
    - apk add --no-cache --update gettext bash curl make
    # Attempts to pull existing image, if doesn't exist then build it
    - make ci-container BRANCH=$CI_COMMIT_REF_NAME IMAGE_TAG=$IMAGE_TAG

.base-template: &base-template
  image: $IMAGE_TAG
  services:
    - redis:latest
    - mysql:latest
  except:
    - schedules

Python Unit Tests:
  <<: *base-template
  stage: test
  script:
    - make test

Database Migration Test:
  <<: *base-template
  stage: test
  script:
    - mysql -u $MYSQL_DATABASE -p$MYSQL_ROOT_PASSWORD -h mysql -e "create database caseapp"
    - python manage.py db upgrade
    - python manage.py populate_db
    - python manage.py db downgrade
```

Originally we had a lengthy before_script that each job used. Each job took a few minutes to execute all the steps. Caching the /var/cache directories helped, but it still took time to install everything.

Eventually we decided on making a docker image as the first step, and reusing that image for each job.

`make ci-container` originally was a quick one-liner to build the docker container and push it to the registry:

```
- cat tests/ci-dockerfile | envsubst > Dockerfile && docker build -t $IMAGE_TAG -f Dockerfile . && rm Dockerfile
- docker push $IMAGE_TAG
```
Some troubles arose when trying to get our gitlab secret variables in the docker build process. That's where envsubst comes into play.


## Deploy merge requests
<img src="/images/post_gitlab_pipelines/gitlab_deployment.png" style="width: 700px;">

```yaml
.deploy-template: &deploy-template
  stage: deploy
  when: manual
  image: thatarchguy/terraform-packer
  variables:
    USER: ubuntu
  before_script:
    - apk add --no-cache --update ansible
    - mkdir ~/.ssh/
    - aws s3api get-object --bucket configs --key keys/deploy.key ~/.ssh/id_rsa && chown 400 ~/.ssh/id_rsa
    - eval "$(ssh-agent -s)"
    - ssh-add ~/.ssh/id_rsa

Deploy review to qa:
  <<: *deploy-template
  script:
    - aws s3api get-object --bucket configs --key app/app.conf.pilot /etc/app.conf
    - cd ops && bash deploy.sh -t qa -c /etc/app.conf -r $CI_COMMIT_REF_SLUG
  environment:
    name: review/$CI_COMMIT_REF_SLUG
    url: https://$CI_COMMIT_REF_SLUG.qa.app.example.com
    on_stop: Stop review app
  only:
    - branches
  except:
    - master
    - pilot
    - schedules

Stop review app:
  <<: *deploy-template
  script:
    - cd ops && bash deploy.sh -t qa -r $CI_COMMIT_REF_SLUG -d
  environment:
    name: review/$CI_COMMIT_REF_SLUG
    action: stop
  only:
    - branches
  except:
    - master
    - pilot
    - schedules
```
One of the cool new features of Terraform is [workspaces](https://www.terraform.io/docs/state/workspaces.html). This allows us to create entirely separate infrastructure for different branches. Interpolating the branch name allows terraform to avoid conflicts.

`deploy.sh` is a script that wraps commands from my previous post, [modern deployment pipelines]({% post_url 2016-08-01-modern-deployment-pipeline %}).

```shell
#...packer build commands here to get $AMI...
terraform init
terraform workspace select "$REVISION"
terraform plan -var "ami=$AMI" -var "revision=$REVISION" deploy
terraform apply -var "ami=$AMI" -var "revision=$REVISION" deploy
```

excerpt of `-d` option:

```shell
terraform init
terraform workspace select "$REVISION"
terraform plan -destroy
terraform destroy -force
terraform workspace select default
terraform workspace delete "$REVISION"
```

This has allowed us to completely migrate off of Jenkins. A year ago [I wrote about a pipeline I was using]({% post_url 2016-08-01-modern-deployment-pipeline %}). Being reliant on an old Jenkins server and not controlling all of the process through code was annoying. Jenkins Pipelines attempts to alleviate this, but it wasn't up to snuff for me.

Having the flexibility to codify complex stages and deploys easily in Gitlab has boosted our efficiency. We can focus more on getting features out to clients and less on maintaining all the moving ops parts.


Do you have any other Gitlab tricks or want more information on mine? Email or hit me up on twitter [@thatarchguy](https://twitter.com/thatarchguy)
