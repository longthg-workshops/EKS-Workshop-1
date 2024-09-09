---
title: "Microservices trên Kubernetes"
date: "`r Sys.Date()`"
weight: 5
chapter: false
pre: "<b> 5. </b>"
---

#### Microservices trên Kubernetes

Bây giờ khi chúng ta đã quen với kiến trúc tổng quan của ứng dụng mẫu, chúng ta sẽ bắt đầu triển khai vào **EKS** như thế nào? Hãy tìm hiểu một số cách xây dựng **Kubernetes** bằng cách xem **component** **catalog**:

![EKS](../images/part5/00019.png?featherlight=false&width=60pc)

Có một số điều cần xem xét trong biểu đồ này:

- Ứng dụng cung cấp **catalog API** chạy như một **Pod**, đây là đơn vị triển khai nhỏ nhất trong Kubernetes. Các **Application Pods** sẽ chạy các **container images** mà chúng ta đã trình bày trong phần trước đó.
- Các **Pods** chạy **catalog component** được tạo bởi một **Deployment** có thể quản lý một hoặc nhiều "bản sao" của **catalog Pod**, cho phép nó mở rộng theo chiều ngang.
- Một **Service** là một cách trừu tượng để tiết lộ một ứng dụng đang chạy dưới dạng một tập hợp các **Pods**, và điều này cho phép **catalog API** của chúng ta được gọi bởi các **components** khác bên trong **Kubernetes cluster**. Mỗi **Service** đều có một mục nhập DNS riêng.
- Chúng ta bắt đầu **workshop** này với một cơ sở dữ liệu **MySQL** chạy bên trong **Kubernetes cluster** của chúng tôi dưới dạng một **StatefulSet**, được thiết kế để quản lý các **stateful workloads**.
- Tất cả các **Kubernetes constructs** này được nhóm lại trong **Namespace** **catalog** riêng của chúng. Mỗi **application component** đều có **Namespace** riêng của nó.

Mỗi trong số các **components** trong kiến trúc **microservices** là một cách khái niệm tương tự **catalog**, sử dụng **Deployments** để quản lý **application workload Pods** và **Services** để định tuyến lưu lượng đến các **Pods** đó. Nếu chúng ta mở rộng tầm nhìn của mình về kiến trúc, chúng ta có thể xem xét cách lưu lượng được định tuyến trong toàn hệ thống rộng lớn hơn:

![EKS](../images/part5/00020.png?featherlight=false&width=60pc)

**Component ui** nhận yêu cầu HTTP từ, ví dụ, trình duyệt của người dùng. Sau đó, nó thực hiện các yêu cầu HTTP đến các **API components** khác trong kiến trúc để thực hiện yêu cầu đó và trả về một phản hồi cho người dùng. Mỗi trong số các **downstream components** có thể có **data stores** hoặc cơ sở hạ tầng khác của riêng mình. **Namespaces** là một nhóm logic của các **resources** cho mỗi **microservice** và cũng hoạt động như một ranh giới cách ly mềm, có thể được sử dụng để triển khai hiệu quả các điều khiển sử dụng **Kubernetes RBAC** và **Network Policies**.