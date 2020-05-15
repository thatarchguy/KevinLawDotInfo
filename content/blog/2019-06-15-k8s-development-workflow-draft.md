---
title: "K8s Development Workflow Using Draft"
date: "2019-06-15T17:15:12-04:00"
author: Kevin L
tags:
- devops
- minikube
- Kubernetes
- draft
- helm
categories:
- Kubernetes
---


**Update 2020-04-23**  
It seems like Draft has been abandoned. One of the maintainers commented:
>"Apologies for the delay on a response. Right now, our team has been re-allocated to other projects (Helm 3 being one of them) and we don't have the time to move Draft further than where it is at the moment. I hope we can get back to it at some point. [..]" [Github](https://github.com/Azure/draft/issues/957#issuecomment-618495363)

So take this blog post with a grain of salt. Technology moves extremely fast in this space.

---

Developing applications for Kubernetes (K8s) can be difficult because the development feedback loop takes a long time. You modify your application, build the docker image, push the image to a repo, redeploy the helm chart, and _finally_ check the deployment to see if it's okay. If you're using Continuous integration to do that for you, then you have the extra steps of committing to version control and pushing to your pipeline.

Enter [Draft.sh](https://draft.sh/)

_"Draft targets the "inner loop" of a developer's workflow: as they hack on code, but before code is committed to version control."_

### Simplified Development

<figure>
<img src="/images/post_draftsh/draft_diagram.png" style="width: 700px;">
</figure>


Using two simple commands, developers can now begin hacking on container-based applications qithout requiring docker or even installing Kubernetes themselves.

`draft create`
Generates a Dockerfile and helm chart for you based on the project's language. These are customizable and can be made with templates called "packs".

`draft up`
Builds the Docker image and deploys to a local Kubernetes using Helm. Any changes made locally will be re-deployed in seconds.


## Trying it out

### Install a local K8s cluster

We'll be using Minikube for our local kubernetes cluster:
```bash
$ pacman -S minikube
$ minikube start
$ eval $(minikube docker-env)
```

The `eval` is important because we are not going to be pushing to an external Docker registry. This command allows us to use minikube's docker daemon and push our image to it. If you leave out this command then Draft will fail to push an image.

### Prepare a Golang App
Make a new project directory and create a golang application. I’ve used a simple server:

```go
package main

import (
	"fmt"
	"net/http"
)

func main() {
	http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
		fmt.Fprintf(w, "Hello, you've requested: %s\n", r.URL.Path)
	})

	http.ListenAndServe(":80", nil)
}
```

### Install Draft
Download the draft binary for your OS and move it into your path. After that you will need to run draft init:

```bash
$ draft init
```
This spins up a draftd deployment and some draft pods in your kube-system namespace. These help manage the connection with the docker image repository.


### Deploy Your App

```bash
$ draft create
```
This command scaffolds a Helm chart (`/chart`), Dockerfile, and a `draft.toml` configuration file. The `draft.toml` is used to define where your app is installed on the cluster and specify different environments if needed.

It's important to note that you can bring your existing helm chart and Dockerfile into a project that you want to use Draft with.

If you change the `watch` property to `true`, it will also look for changes on your local filesystem and redeploy the app on every change:

```toml
[environments]
  [environments.development]
    name = "testproject"
    namespace = "default"
    wait = true
    watch = true
    watch-delay = 2
    auto-connect = false
    dockerfile = "Dockerfile"
    chart = ""
```

If the file looks good, we can deploy the application:
```bash
$ draft up
  Draft Up Started: 'testproject': 01DE7K7N6N56BNTZW3TGFDMYXT
  testproject: Building Docker Image: SUCCESS ⚓   (27.0273s)
  testproject: Releasing Application: SUCCESS ⚓   (2.4574s)
  Inspect the logs with `draft logs 01DE7K7N6N56BNTZW3TGFDMYXT`

Watching local files for changes...
```

You should be able to see the application deployed using helm:
```bash
$ helm list
NAME       	REVISION	UPDATED                 	STATUS  	CHART             	APP VERSION	NAMESPACE
testproject	1       	Tue Jun 15 11:22:42 2019	DEPLOYED	testproject-v0.1.0	           	default
```

And you should be able to see it deployed using `kubectl`
```bash
$ kubectl get pods
NAME                                       READY   STATUS    RESTARTS   AGE
testproject-testproject-859b866878-sbwk4   1/1     Running   0          4m19s
```

You can port forward to your local system if you'd like:

```bash
$ kubectl port-forward testproject-testproject-859b866878-sbwk4 8080:8080
```

<figure>
<img src="/images/post_draftsh/app_screenshot.png" style="width: 300px;">
</figure>

What's incredible is that you can make changes locally and draft will automatically re-deploy your app in seconds.

Hopefully it all worked for you! It's imperative to have identical setups in development and production. You wouldn't want to be developing using vagrant and deploying using helm. Draft lets you rapidly develop locally with a fast feedback loop with the same tooling that is used in production.
