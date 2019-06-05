---
title: 'Using K3s and Helm for Local Kubernetes Fun'
date: '2019-06-01T19:21:04-05:00'
author: Kevin L
tags:
- devops
- K3s
- Kubernetes
categories:
- Kubernetes
---

The industry is moving towards containers and [Kubernetes](https://kubernetes.io/) (K8s) won the rat race to become the standard platform. However, K8s is a daunting platform to run. Many projects try to make it easier like Rancher and Amazon's EKS. Running it locally for development is even harder, but [Minikube](https://github.com/kubernetes/minikube) made it possible with virtual machines. Now we have a new player coming out of Rancher Labs called [K3s](https://k3s.io/).

_"Easy to install. A binary of less than 40 MB. Only 512 MB of RAM required to run."_

Perfect for Raspberry Pis and local development purposes.

How does it take a big complex platform like Kubernetes and bundle it down to a 40MB binary on 512MB of RAM?

 - Removes Legacy, alpha, non-default features.
 - Removes in-tree plugins (cloud providers and storage plugins) that can be added in
 - Replaces etcd3 with Sqlite3
 - Adds TLS management
 - Comes Wrapped in a simple launcher

<figure>
<img src="https://k3s.io/img/how-it-works.svg" style="width: 900px;">
<figcaption><i>How K3s works</i></figcaption>
</figure>

Excited yet? Let's get started.

Since it's written in [Go](https://golang.org/), it's all contained in a single binary. You can download it from the [releases page](https://github.com/rancher/k3s/releases/latest) or use your [package manager](https://aur.archlinux.org/packages/k3s-bin/).

```
k3s server
```

If you run into errors with containerd, try using docker (not recommended):
```bash
k3s server --docker
```

You should be able to get some basic information now
```bash
$ export KUBECONFIG=/etc/rancher/k3s/k3s.yaml

$ kubectl cluster-info
Kubernetes master is running at https://localhost:6443
CoreDNS is running at https://localhost:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.

```

That's it. You are now running Kubernetes on your machine.

Let's start playing with it.

### Helm Basics

<figure>
<img src="/images/post_k3s/pipeline.png" style="width: 500px;">
</figure>

Helm is the standard for deploying applications on K8s. Think of it as the package manager for Kubernetes.

Helm packages are called **"charts"** and deployments are called **"releases"**.

They can define much more than just simple putting your application on K8s. Helm defines your deployment as code by specifying things like **replication count**, **anti-affinity**, **lifecycle hooks**, and **dependent charts**. Templates can be customized for different environments like production, pilot, and local development. Which makes it more powerful than running something like `kubectl run myapp --image=localhost:5000/myapp`.


```bash
# Installs tiller on K8s, should already be installed though
helm init

# Updates Repo
helm repo update
```

There's some useful charts you can install to help with local development:
```bash
helm install stable/redis
helm install stable/postgresql
helm install stable/nats
```

You can run some home automation applications on K3s using Helm. Which makes running a [little raspberry pi K3s cluster](https://blog.alexellis.io/test-drive-k3s-on-raspberry-pi/) a cool weekend project.

 - [stable/unifi](https://github.com/helm/charts/tree/master/stable/unifi)
 - [stable/home-assistant](https://github.com/helm/charts/tree/master/stable/home-assistant)
 - [stable/owncloud](https://github.com/helm/charts/tree/master/stable/owncloud)

Use `helm search` and [Helm Hub](https://hub.helm.sh/) to find more!

### Developing with Helm

https://helm.sh/docs/chart_template_guide/#a-starter-chart

Okay so you have an application that you want deployed on K3s. Let's just say it's something really simple like `python3 -m http.server`.

#### Docker build
Add a simple Dockerfile

```Dockerfile
# Dockerfile
FROM python:alpine

EXPOSE 8888

ENTRYPOINT ["python", "-m", "http.server", "8888"]
```

We can build our image and push it to a respository.
```bash
$ docker build -t registry.gitlab.com/thatarchguy/myapp:latest .
$ docker push registry.gitlab.com/thatarchguy/myapp:latest
```

I use Gitlab's free registry, but if you need a local docker registry you can spin one up in k3s by doing the following:
([Source](https://blog.hasura.io/sharing-a-local-registry-for-minikube-37c7240d0615/))
```bash
$ kubectl apply -f https://gist.githubusercontent.com/coco98/b750b3debc6d517308596c248daf3bb1/raw/6efc11eb8c2dce167ba0a5e557833cc4ff38fa7c/kube-registry.yaml
$ kubectl port-forward --namespace kube-system $(kubectl get po -n kube-system | grep kube-registry-v0 | \awk '{print $1;}') 5000:5000
$ docker build -t localhost:5000/myapp:latest .
$ docker push localhost:5000/myapp:latest
```
We will assume you're using this local registry for the rest of the guide.


#### Helm create
```bash
$ mkdir charts && cd charts
$ helm create myapp
$ cd myapp
```

Now we have a base chart for our application. Edit the `values.yaml` file to include the registry and docker image you just pushed for the application
```yaml
[...]
image:
  repository: localhost:5000/myapp
  tag: latest
  pullPolicy: Always
[...]
```

Now let's deploy it!

#### Helm apply

```bash
$ cd ../
$ helm upgrade --install myapp myapp
Release "myapp" does not exist. Installing it now.
NAME:   myapp
LAST DEPLOYED: Tue Jun  4 14:17:59 2019
NAMESPACE: default
STATUS: DEPLOYED
[...]


$ kubectl describe pod myapp-6bf57b7bbd-82qkt
Name:               myapp-6bf57b7bbd-82qkt
Namespace:          default
Priority:           0
PriorityClassName:  <none>
Node:               minikube/10.0.2.15
Start Time:         Tue, 04 Jun 2019 14:17:59 -0400
Labels:             app.kubernetes.io/instance=myapp
                    app.kubernetes.io/name=myapp
                    pod-template-hash=6bf57b7bbd
[...]
```

To destroy and purge the release:
```bash
$ helm delete --purge myapp
```

Hopefully it all worked for you! This is the same process you would use to deploy a big Kubernetes cluster in production. It's imperative to have identical setups in development and production. You wouldn't want to be developing using vagrant and deploying using helm. K3s allows us to run whole Kubernetes clusters on a tiny laptop and develop without spinning our fans.

K3s also brings Kubernetes to people who may not be able get their hands on powerful servers or can pay for the cloud. They can run their desktop off a raspberry pi and K3s on another.