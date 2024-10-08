---
title: "Lab Components"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: "<b> 3.2 </b>"
---

Before a workload can be deployed to a Kubernetes distribution like EKS it first must be packaged as a container image and published to a container registry. Basic container topics like this are not covered as part of this workshop, and the sample application has container images already available in Amazon Elastic Container Registry for the labs we'll complete today.

The table below provides links to the ECR Public repository for each component, as well as the `Dockerfile` that was used to build each component.

| Component     | ECR Public repository                                                             | Dockerfile                                                                                                  |
| ------------- | --------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------- |
| UI            | [Repository](https://gallery.ecr.aws/aws-containers/retail-store-sample-ui)       | [Dockerfile](https://github.com/aws-containers/retail-store-sample-app/blob/0.1.0/images/java17/Dockerfile) |
| Catalog       | [Repository](https://gallery.ecr.aws/aws-containers/retail-store-sample-catalog)  | [Dockerfile](https://github.com/aws-containers/retail-store-sample-app/blob/0.1.0/images/go/Dockerfile)     |
| Shopping cart | [Repository](https://gallery.ecr.aws/aws-containers/retail-store-sample-cart)     | [Dockerfile](https://github.com/aws-containers/retail-store-sample-app/blob/0.1.0/images/java17/Dockerfile) |
| Checkout      | [Repository](https://gallery.ecr.aws/aws-containers/retail-store-sample-checkout) | [Dockerfile](https://github.com/aws-containers/retail-store-sample-app/blob/0.1.0/images/nodejs/Dockerfile) |
| Orders        | [Repository](https://gallery.ecr.aws/aws-containers/retail-store-sample-orders)   | [Dockerfile](https://github.com/aws-containers/retail-store-sample-app/blob/0.1.0/images/java17/Dockerfile) |
| Assets        | [Repository](https://gallery.ecr.aws/aws-containers/retail-store-sample-assets)   | [Dockerfile](https://github.com/aws-containers/retail-store-sample-app/blob/0.1.0/src/assets/Dockerfile)    |