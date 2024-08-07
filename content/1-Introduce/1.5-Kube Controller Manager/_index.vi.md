---
title: "Kube Controller Manager"
date: "`r Sys.Date()`"
weight: 5
chapter: false
pre: "<b> 1.5 </b>"
---

#### **Kube Controller Manager**

- Trình quản lý bộ điều khiển Kube (**Kube Controller Manager**) quản lý các bộ điều khiển khác nhau trong Kubernetes. Trong thuật ngữ của Kubernetes, một bộ điều khiển là một quá trình liên tục theo dõi trạng thái của các thành phần bên trong hệ thống và làm việc nhằm đưa toàn bộ hệ thống về trạng thái hoạt động mong muốn.

![Kubernetes](/EKS-Workshop-1/images/part1/5/0006.png?featherlight=false&width=60pc)

#### **Bộ Điều Khiển Nút (Node Controller)**

- Chịu trách nhiệm giám sát trạng thái của các Nút (**Nodes**) và thực hiện các hành động cần thiết để duy trì ứng dụng hoạt động.

#### **Bộ Điều Khiển Sao Chép (Replication Controller)**

- Chịu trách nhiệm theo dõi trạng thái của các replica sets và đảm bảo số lượng pods mong muốn luôn sẵn có trong bộ.

#### **Cài Đặt Trình Quản Lý Bộ Điều Khiển Kube (Kube-Controller-Manager)**

- Khi bạn cài đặt **kube-controller-manager**, các bộ điều khiển khác nhau cũng sẽ được cài đặt.
- Tải xuống nhị phân **kube-controller-manager** từ trang phát hành Kubernetes. Ví dụ: Bạn có thể tải xuống **kube-controller-manager** v1.13.0 tại đây [kube-controller-manager](https://storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kube-controller-manager)

```
$ wget https://storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kube-controller-manager
```

Mặc định, tất cả các bộ điều khiển đều được bật, nhưng bạn có thể chọn bật một số bộ điều khiển cụ thể từ **kube-controller-manager.service**

```
$ cat /etc/systemd/system/kube-controller-manager.service
```

#### **Xem kube-controller-manager - kubeadm**

- **Kubeadm** triển khai **kube-controller-manager** dưới dạng một pod trong namespace kube-system

```
$ kubectl get pods -n kube-system
```

- Xem các tùy chọn **kube-controller-manager - kubeadm**
- Bạn có thể xem các tùy chọn trong pod tại `/etc/kubernetes/manifests/kube-controller-manager.yaml`

```
$ cat /etc/kubernetes/manifests/kube-controller-manager.yaml
```

- Xem các tùy chọn **kube-controller-manager - Thủ công**
- Trong cài đặt không phải **kubeadm**, bạn có thể kiểm tra các tùy chọn bằng cách xem **kube-controller-manager.service**

```
$ cat /etc/systemd/system/kube-controller-manager.service
```

- Bạn cũng có thể xem quá trình đang chạy và các tùy chọn hiệu quả bằng cách liệt kê quá trình trên nút master và tìm kiếm **kube-controller-manager**.

```
$ ps -aux | grep kube-controller-manager
```

#### **Tài Liệu Tham Khảo K8s:**

- [Kubernetes Command Line Tools Reference - kube-controller-manager](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-controller-manager/)
- [Kubernetes Concepts Overview - Components](https://kubernetes.io/docs/concepts/overview/components/)
