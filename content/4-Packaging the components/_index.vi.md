---
title: "Các thành phần"
date: "`r Sys.Date()`"
weight: 5
chapter: false
pre: "<b> 4. </b>"
---

#### Các thành phần

Trước khi một công việc có thể được triển khai lên một bản phân phối **Kubernetes** như **EKS**, nó trước tiên phải được đóng gói như một hình ảnh container và xuất bản vào một registry container. Các chủ đề cơ bản về container như này không được bao gồm trong phần workshop này, và ứng dụng mẫu đã có các hình ảnh container sẵn có trong **Amazon Elastic Container Registry** cho các lab mà chúng ta sẽ hoàn thành hôm nay.

Bảng dưới đây cung cấp các liên kết đến kho chứa công cộng **ECR** cho mỗi thành phần, cũng như `Dockerfile` đã được sử dụng để xây dựng từng thành phần.

| Component | ECR Public repository | Dockerfile |
|---------------|------|------|
| UI | [Repository](https://gallery.ecr.aws/aws-containers/retail-store-sample-ui) | [Dockerfile](https://github.com/aws-containers/retail-store-sample-app/blob/main/images/java17/Dockerfile) |
| Catalog | [Repository](https://gallery.ecr.aws/aws-containers/retail-store-sample-catalog) | [Dockerfile](https://github.com/aws-containers/retail-store-sample-app/blob/main/images/go/Dockerfile) |
| Shopping cart | [Repository](https://gallery.ecr.aws/aws-containers/retail-store-sample-cart) | [Dockerfile](https://github.com/aws-containers/retail-store-sample-app/blob/main/images/java17/Dockerfile) |
| Checkout | [Repository](https://gallery.ecr.aws/aws-containers/retail-store-sample-checkout) | [Dockerfile](https://github.com/aws-containers/retail-store-sample-app/blob/main/images/nodejs/Dockerfile) |
| Orders | [Repository](https://gallery.ecr.aws/aws-containers/retail-store-sample-orders) | [Dockerfile](https://github.com/aws-containers/retail-store-sample-app/blob/main/images/java17/Dockerfile) |
| Assets | [Repository](https://gallery.ecr.aws/aws-containers/retail-store-sample-assets) | [Dockerfile](https://github.com/aws-containers/retail-store-sample-app/blob/main/src/assets/Dockerfile) |