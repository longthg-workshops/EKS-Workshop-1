---
title: "Sample Aplication"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: "<b> 3.1 </b>"
---

Most of the labs in this workshop use a common sample application to provide actual container components that we can work on during the exercises. The sample application models a simple web store application, where customers can browse a catalog, add items to their cart and complete an order through the checkout process.

![EKS](../../images/part3/00017.png?featherlight=false&width=90pc)

The application has several components and dependencies:

![EKS](../../images/part3/00018.png?featherlight=false&width=60pc)

| Component     | Description                                                                                   |
| ------------- | --------------------------------------------------------------------------------------------- |
| **UI**            | Provides the front end user interface and aggregates API calls to the various other services. |
| **Catalog**       | API for product listings and details                                                          |
| **Cart**          | API for customer shopping carts                                                               |
| **Checkout**      | API to orchestrate the checkout process                                                       |
| **Orders**        | API to receive and process customer orders                                                    |
| **Static assets** | Serves static assets like images related to the product catalog                               |

Initially we'll deploy the application in a manner that is self-contained in the Amazon EKS cluster, without using any AWS services like load balancers or a managed database. Over the course of the labs we'll leverage different features of EKS to take advantage of broader AWS services and features for our retail store.

You can find the full source code for the sample application on [GitHub](https://github.com/aws-containers/retail-store-sample).