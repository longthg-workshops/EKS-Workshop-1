---
title: "Kube Proxy"
date: "`r Sys.Date()`"
weight: 8
chapter: false
pre: "<b> 1.8 </b>"
---

#### Kube Proxy

Trong cụm **Kubernetes**, mỗi **pod** có thể kết nối với mọi **pod** khác, điều này được thực hiện bằng cách triển khai một **cụm mạng pod** vào cụm.

**Kube-Proxy** là một quy trình chạy trên mỗi **node** trong cụm **Kubernetes**.

![Kubernetes](/EKS-Workshop-1/images/part1/8/0008.png?featherlight=false&width=60pc)

- Cài đặt **kube-proxy** - Thủ công
- Tải xuống tệp nhị phân **kube-proxy** từ trang phát hành **Kubernetes** tại [đây](kube-proxy). Ví dụ: Để tải về **kube-proxy** v1.13.0, chạy lệnh dưới đây.

```bash
$ wget https://storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kube-proxy
```

- Giải nén và chạy nó như một dịch vụ.

#### Xem các tùy chọn **kube-proxy** - **kubeadm**

- Nếu bạn thiết lập nó với công cụ **kubeadm**, công cụ **kubeadm** sẽ triển khai **kube-proxy** dưới dạng **pod** trong không gian tên **kube-system**. Thực tế, nó được triển khai dưới dạng một **daemonset** trên **node** chủ.

```bash
$ kubectl get pods -n kube-system
```

Đó là một phần quan trọng trong việc quản lý **cụm Kubernetes** và đảm bảo sự kết nối mạng chính xác giữa các **pod** trong **cụm**.