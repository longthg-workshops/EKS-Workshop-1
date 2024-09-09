---
title: "Pods"
date: "`r Sys.Date()`"
weight: 9
chapter: false
pre: "<b> 1.9 </b>"
---

In the **Kubernetes** system, **Pods** are an important concept. Kubernetes does not deploy containers directly to worker nodes. Instead, Kubernetes uses Pods to manage containers.

Each Pod in Kubernetes contains one or more containers, but typically they consist of a single container each, and that is the instance of the application you are running. Pods will have a one-to-one relationship with the containers running your application.

![Kubernetes Pods](../../images/part1/9/0009.png?featherlight=false&width=60pc)

### Multi-Container PODs
- A single pod can have multiple containers except for the fact that they are usually not multiple containers of the **`same kind`**.

### How to deploy pods?
Lets now take a look to create a nginx pod using **`kubectl`**.

- To deploy a docker container by creating a POD.
```
$ kubectl run nginx --image nginx
```

- To get the list of pods
```
$ kubectl get pods
```

### K8s Reference Docs

- [Kubernetes - Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/)
- [Kubernetes - Pod Overview](https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/)
- [Kubernetes - Explore Introduction](https://kubernetes.io/docs/tutorials/kubernetes-basics/explore/explore-intro/)