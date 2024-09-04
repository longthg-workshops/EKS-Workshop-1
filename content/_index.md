---
title: "Kubernetes on AWS"
date: "`r Sys.Date()`"
weight: 1
chapter: false
---

# Kubernetes on AWS

Kubernetes is a flexible, scalable, and **open-sourced** platform for containerized applications and associated services orchestration, making it easier to configure and automate the application development process. Known as a large and rapidly growing ecosystem, Kubernetes provides extensive support for a variety of services and tools.

The name **Kubernetes** comes from the Greek word for helmsman/pilot. Kubernetes was released to the public in 2014 by Google, based on Google's recent decade of real-world workload management experience, combined with ideas and **best practices** from the community.

#### Back in Time

Let's take a look at why Kubernetes matters by looking back.

![Kubernetes](/EKS-Workshop-1/images/part1/00010.svg?featherlight=false&width=60pc)

**Traditional deployment period:** Initially, applications runs directly on a logical server, making resource allocation difficult because there was no mechanism to define resource boundaries for each application. This approach led to the dangerous foundation that an application could use too many resources, affecting the operation of other applications. The solution was to run each application on a special server, but this was not cost-effective and resource-efficient.

**Virtualization deployment period:** Virtualization was introduced as a solution that allowed running multiple virtual machines (VMs) on the same server, helping her install applications and enhance security. Virtualization also helps improve resource efficiency and scalability.

**Container Development Era:** Containers are like VMs But are lighter and share the Operating System (OS) with each other. Containers provide many benefits such as rapid application creation and deployment, continuous development and deployment, clear separation between development and operations, best-in-class features across environments, portability between clouds and operating systems, and centralized application management.

#### Why do you need Kubernetes and what can it do?

Containers are a convenient way for you to contribute and run applications. In a production environment, there needs to be an efficient mechanism for managing containers, ensuring no downtime. Kubernetes helps manage powerful distributed systems, automates scaling, provides declarative development patterns, and much more.

Kubernetes provides:

- **Service discovery and load balancing** 
- **Storage orchestration** 
- **Automatic rollouts and rollbacks** 
- **Automatic packaging** 
- **Self-healing** 
- **Configuration management and security** 

#### Kubernetes is not... 

Kubernetes is not a traditional, full-blown **PaaS** system. It operates at the container layer, providing **PaaS**-like features such as deployment, replication, load balancing, but is a flexible and scalable solution that does not limit the type of applications supported, does not require source code development or application building, does not provide high-level application provisioning services such as **middleware**, **databases**, does not require logging, monitoring, or alerting solutions, and does not provide or enforce any configuration, maintenance, management, or automatic recovery of the system. Kubernetes eliminates the need for traditional orchestration, instead providing continuous control from current state to desired state.