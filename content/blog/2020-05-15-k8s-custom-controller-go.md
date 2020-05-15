---
title: "Writing a K8s Controller in Go"
date: "2020-05-14T10:54:09-04:00"
author: Kevin L
tags:
- devops
- K8s
- Kubernetes
categories:
- Kubernetes
---

Kubernetes is a complicated but powerful platform out of the box. One thing that engineers often overlook is customizing it. Kubernetes was made to be extendable. It lets you add modifications to change the behavior of the cluster.

You may have a need for a custom controller to watch for events and take an action. For example, [kubewatch](https://github.com/bitnami-labs/kubewatch) lets you watch for resource changes and notify to your chat program of choice.

The [Kubernetes Go client](https://github.com/kubernetes/client-go/) lets you write your own controllers easily.

First we need to understand what is going on under the hood with client-go and a custom controller. Kubernetes has documentation on the [concepts of controllers](https://kubernetes.io/docs/concepts/architecture/controller/), but I think this diagram does a better job at visualizing it.

<figure>
<img src="/images/post_k8s_custom_controller_go/controller.jpg" style="width: 700px;">
<figcaption><i>Kubernetes client-go and Custom Controller</i>
</figcaption>
</figure>

We can break that down into two sections:
 - Components that the custom controller has to provide
 - Components that client-go provides

__Custom Controller components__

1. _Informer reference_: This is the reference to the Informer instance that knows how to work with your custom resource objects. Your custom controller code needs to create the appropriate Informer.
1. _Indexer reference_: This is the reference to the Indexer instance that knows how to work with your custom resource objects. client-go provides functions to create Informer and Indexer according to your needs. In your code you can either directly invoke these functions or use factory methods for creating an informer.
1. _Resource Event Handlers_: These are the callback functions which will be called by the Informer when it wants to deliver an object to your controller.

__client-go components__  
These are things you do not have to worry about, but should know how they work.

1. _Reflector_: A reflector watches the Kubernetes API for the specified resource type (kind). This could be an in-built resource or it could be a custom resource. When it receives notification about existence of new resource instance through the watch API, it gets the newly created object using the corresponding listing API. It then puts the object in a Delta Fifo queue.
1. _Informer_: An informer pops objects from the Delta Fifo queue. Its job is to save object for later retrieval, and invoke the controller code passing it the object.
1. _Indexer_: An indexer provides indexing functionality over objects. A typical indexing use-case is to create an index based on object labels. Indexer can maintain indexes based on several indexing functions. Indexer uses a thread-safe data store to store objects and their keys. There is a default function that generates an object’s key as<namespace>/<name> combination for that object.

You can follow along with this blog post using your own K8s cluster, [Minikube](https://github.com/kubernetes/minikube), or [k3s](https://k3s.io/).

Lets start by instantiating our client

```go
func main() {
	var kubeconfig *string
	if home := homedir.HomeDir(); home != "" {
		kubeconfig = flag.String("kubeconfig", filepath.Join(home, ".kube", "config"), "(optional) absolute path to the kubeconfig file")
	} else {
		kubeconfig = flag.String("kubeconfig", "", "absolute path to the kubeconfig file")
	}
	flag.Parse()

	config, err := clientcmd.BuildConfigFromFlags("", *kubeconfig)
	if err != nil {
		panic(err)
	}
	clientset, err := kubernetes.NewForConfig(config)
	if err != nil {
		panic(err)
	}
}
```

This will allow us to run the program locally as well as in the cluster. The next stage is setting up the informer and resource event handler functions. We want to listen for [deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) operations in the cluster.


```go
factory := informers.NewSharedInformerFactoryWithOptions(clientset, 0, informers.WithNamespace(metav1.NamespaceAll))
informer := factory.Apps().V1().Deployments().Informer()
informer.AddEventHandler(cache.ResourceEventHandlerFuncs{
  // When a new deployment gets created
  AddFunc: func(obj interface{}) { panic("not implemented") },
  // When a deployment gets deleted
  DeleteFunc: func(interface{}, interface{}) { panic("not implemented") },
  // When a deployment gets updated
	UpdateFunc: func(interface{}) { panic("not implemented") },
})
```

Now we have an informer that can listen for new deployments. We provide `metav1.NamespaceAll()` so it listens on all namespaces. If you want a specific one you can replace it with `"my-namespace"`.

Let's flesh out the `UpdateFunc` to change the image in the deployment to something different.
```go
UpdateFunc: func(oldObj, newObj interface{}) {
	deployment := newObj.(*appsv1.Deployment)
	oldDepl := oldObj.(*appsv1.Deployment)
	if deployment.ResourceVersion == oldDepl.ResourceVersion {
		// Periodic resync will send update events for all known Deployments.
		// Two different versions of the same Deployment will always have different RVs.
		return
	}
	fmt.Println("Deployment Update Found: ", deployment.Name)
	fmt.Println("Updating Image", deployment.Spec.Template.Spec.Containers[0].Image, "with nginx:1.13")
	deployment.Spec.Template.Spec.Containers[0].Image = "nginx:1.13"
	deploymentsClient := clientset.AppsV1().Deployments(deployment.Namespace)
	_, err := deploymentsClient.Update(deployment)
	if err != nil {
		//panic(err)
	}
	fmt.Println("Updated deployment: ", deployment.Name)
	fmt.Println("-----")
},
```

We can extend this further by modifying the spec of the deployment to be whatever we want. We can also find and replace text. This comes in handy when grabbing values from an external source. We will leave it as a simple `image` replace for this example.

We can use a simple Kubernetes deployment to try this out:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
  labels:
    app: my-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: test
          image: alpine:latest
          args:
            - sh
            - -c
            - while true; do sleep 5; done
```

Run the program using `go run main.go`, and in another terminal apply the Kubernetes YAML with `kubectl apply -f my-app.yaml`. You should see a log like this:

```
Deployment Update Found:  my-app
Updating Image alpine:latest with nginx:1.13
Updated deployment:  my-app
```
If you run `kubectl edit deployment my-app` you should see that the image in the spec has been edited.

Writing a controller is that simple! If you want to run this controller in a production-like settings, you'll need a [Dockerfile](https://docs.docker.com/engine/reference/builder/) and simple [helm chart](https://helm.sh/docs/chart_template_guide/getting_started/).

I hope that my explanation of various components involved in writing custom Kubernetes controllers will help you write your own custom controllers. Please feel free to reach out with any questions. The documentation on [client-go](https://github.com/kubernetes/client-go) is very helpful.

Full disclosure, I haven't found a way to fail to deployment. In my personal use case I would like to replace a value with an external source, but if that external source is not found I want to fail the deployment. However, I am having some luck with `k8s.io/client-go/util/retry.RetryOnConflict`.


Full code:
```go
package main

import (
	"flag"
	"fmt"
	"path/filepath"

	appsv1 "k8s.io/api/apps/v1"
	metav1 "k8s.io/apimachinery/pkg/apis/meta/v1"
	"k8s.io/client-go/informers"
	"k8s.io/client-go/kubernetes"
	"k8s.io/client-go/tools/cache"
	"k8s.io/client-go/tools/clientcmd"
	"k8s.io/client-go/util/homedir"
)

func main() {
	var kubeconfig *string

	if home := homedir.HomeDir(); home != "" {
		kubeconfig = flag.String("kubeconfig", filepath.Join(home, ".kube", "config"), "(optional) absolute path to the kubeconfig file")
	} else {
		kubeconfig = flag.String("kubeconfig", "", "absolute path to the kubeconfig file")
	}
	flag.Parse()

	config, err := clientcmd.BuildConfigFromFlags("", *kubeconfig)
	if err != nil {
		panic(err)
	}
	clientset, err := kubernetes.NewForConfig(config)
	if err != nil {
		panic(err)
	}

	factory := informers.NewSharedInformerFactoryWithOptions(clientset, 0, informers.WithNamespace(metav1.NamespaceAll))
	informer := factory.Apps().V1().Deployments().Informer()

	informer.AddEventHandler(cache.ResourceEventHandlerFuncs{
		// When a new pod gets created
		AddFunc: func(obj interface{}) {},
		// When a pod gets deleted
		DeleteFunc: func(obj interface{}) {},
		// When a pod gets updated
		UpdateFunc: func(oldObj, newObj interface{}) {
			deployment := newObj.(*appsv1.Deployment)
			oldDepl := oldObj.(*appsv1.Deployment)
			if deployment.ResourceVersion == oldDepl.ResourceVersion {
				// Periodic resync will send update events for all known Deployments.
				// Two different versions of the same Deployment will always have different RVs.
				return
			}
			fmt.Println("Deployment Update Found: ", deployment.Name)
			fmt.Println("Updating Image", deployment.Spec.Template.Spec.Containers[0].Image, "with nginx:1.13")
			deployment.Spec.Template.Spec.Containers[0].Image = "nginx:1.13"
			deploymentsClient := clientset.AppsV1().Deployments(deployment.Namespace)
			_, err := deploymentsClient.Update(deployment)
			if err != nil {
				//panic(err)
			}
			fmt.Println("Updated deployment: ", deployment.Name)
			fmt.Println("-----")
		},
	})

	stop := make(chan struct{})
	defer close(stop)
	go informer.Run(stop)

	// Wait forever
	select {}
}
```
