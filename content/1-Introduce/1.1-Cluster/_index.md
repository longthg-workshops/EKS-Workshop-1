---
title: "Cluster Architechture"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: "<b> 1.1 </b>"
---

#### **The architectural concepts behind Kubernetes**
A Kubernetes cluster consists of a control plane and one or more worker nodes. Here's a brief overview of the main components:

![Kubernetes Arch](/EKS-Workshop-1/images/part1/1/0002.png?featherlight=false&width=60pc)

**1. Control Plane Components:** Manage the overall state of the cluster:

- **kube-apiserver:** The core component server that exposes the Kubernetes HTTP API

- **etcd:** Consistent and highly-available key value store for all API server data

- **kube-scheduler:** Looks for Pods not yet bound to a node, and assigns each Pod to a suitable node.

- **kube-controller-manager:** Runs controllers to implement Kubernetes API behavior.

- **cloud-controller-manager (optional):** Integrates with underlying cloud provider(s).

**2. Worker Node Components:** Run on every node, maintaining running pods and providing the Kubernetes runtime environment:

- **kubelet:** Ensures that Pods are running, including their containers.

- **kube-proxy (optional):** Maintains network rules on nodes to implement Services.

- **Container runtime:** Software responsible for running containers. Read Container Runtimes to learn more.

3. **Pods**: A Pod is a smallest unit in Kubernetes, which contains at least one container. Containers in the same pod share their storage and network resources.

4. **Services**: Services route requests to pods. Each service is associated with a group of pods and provide unified access to them

5. **Volumes**: Volume is how container store, access and share data on disks.

6. **Namespace**: Namespaces allow you to divide a cluster into smaller part with different resources and configurations

7. **Ingress Controller**: The Ingress Controller manages redirections of HTTP/HTTPS requests to Services in clusters based on rules configurations.

8. **Storage Classes**: Storage classes define different types of storage that can be requested by persistent volume

The coordination of the components above forms an agile and powerful environment on a Kubernetes cluster for application deloyment.
