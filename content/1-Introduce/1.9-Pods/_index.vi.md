---
title: "Pods"
date: "`r Sys.Date()`"
weight: 9
chapter: false
pre: "<b> 1.9 </b>"
---

Trong hệ thống **Kubernetes**, **Pods** là một khái niệm quan trọng. Kubernetes không triển khai trực tiếp các container lên node làm việc (worker node). Thay vào đó, Kubernetes sử dụng Pods để quản lý các container.

Mỗi Pod trong Kubernetes đều chứa một hoặc nhiều container, nhưng thông thường chúng chứa một container đơn, và đó là thể hiện của ứng dụng bạn đang chạy. Pod sẽ có mối quan hệ một-một với các container chạy ứng dụng của bạn.

![Kubernetes Pods](/EKS-Workshop-1/images/part1/9/0009.png?featherlight=false&width=60pc)

### Pod Đa-Container

Một Pod có thể chứa nhiều container, mặc dù thường không phải là nhiều container cùng một loại.

### Cách triển khai Pods?

Bây giờ, hãy xem cách tạo một Pod **nginx** sử dụng **kubectl**.

Để triển khai một container Docker bằng cách tạo một Pod, bạn có thể sử dụng lệnh sau:

```bash
$ kubectl run nginx --image nginx
```

Để lấy danh sách các Pods, bạn có thể sử dụng lệnh:

```bash
$ kubectl get pods
```

### Tài liệu tham khảo Kubernetes:

- [Kubernetes - Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/)
- [Kubernetes - Pod Overview](https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/)
- [Kubernetes - Explore Introduction](https://kubernetes.io/docs/tutorials/kubernetes-basics/explore/explore-intro/)
