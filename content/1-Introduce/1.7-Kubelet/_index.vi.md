---
title: "Kubelet"
date: "`r Sys.Date()`"
weight: 7
chapter: false
pre: "<b> 1.7 </b>"
---

#### Kubelet

Kubelet là một trong những thành phần quan trọng của Kubernetes. Nó chịu trách nhiệm quản lý và duy trì các container chạy trên mỗi node trong cụm Kubernetes.

Mặc định, Kubeadm không triển khai Kubelet. Bạn cần phải tự tải về và cài đặt nó.

![Kubernetes](/EKS-Workshop-1/images/part1/7/0007.png?featherlight=false&width=60pc)

Để cài đặt Kubelet, bạn có thể tải file nhị phân từ trang phát hành Kubernetes [tại đây](https://kubernetes.io/docs/reference/command-line-tools-reference/kubelet/). Ví dụ: Để tải về Kubelet v1.13.0, sử dụng lệnh sau.

```bash
$ wget https://storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kubelet
```

Sau khi tải xuống, giải nén file và chạy nó như một dịch vụ.

Để xem các tùy chọn của Kubelet và quá trình đang chạy, bạn có thể sử dụng lệnh sau trên node worker:

```bash
$ ps -aux | grep kubelet
```

#### Tài liệu tham khảo Kubernetes:

- [Command Line Tools Reference - Kubelet](https://kubernetes.io/docs/reference/command-line-tools-reference/kubelet/)
- [Overview Components](https://kubernetes.io/docs/concepts/overview/components/)
- [Kubeadm Kubelet Integration](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/kubelet-integration/)