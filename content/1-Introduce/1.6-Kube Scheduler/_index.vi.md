---
title: "Kube Scheduler"
date: "`r Sys.Date()`"
weight: 6
chapter: false
pre: "<b> 1.6 </b>"
---

#### Kube Scheduler

#### **kube-scheduler** là gì?

- **kube-scheduler** chịu trách nhiệm lên lịch cho các pod trên các node. **kube-scheduler** chỉ quyết định pod nào sẽ được đặt trên node nào. Nó không thực sự đặt pod lên các node, đó là công việc của **kubelet**.

![Kubernetes](/EKS-Workshop-1/images/part1/6/0006.ppm?featherlight=false&width=60pc)

#### Tại sao bạn cần một Bộ lập lịch (Scheduler)?

- **kube-scheduler** đóng vai trò quan trọng trong việc quản lý tài nguyên và đảm bảo rằng các pod được phân bổ một cách hiệu quả trên các node.

#### Cài đặt **kube-scheduler** - Thủ công

- Để tải xuống nhị phân **kube-scheduler** từ các trang phát hành của Kubernetes, bạn có thể thực hiện như sau. Ví dụ: Để tải xuống **kube-scheduler** v1.13.0, hãy chạy lệnh dưới đây.

```
$ wget https://storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kube-scheduler
```

- Sau đó, giải nén và chạy nó như một dịch vụ.

#### Xem các tùy chọn của **kube-scheduler** - kubeadm

- Nếu bạn thiết lập nó bằng công cụ **kubeadm**, công cụ này sẽ triển khai **kube-scheduler** dưới dạng pod trong namespace **kube-system** trên node master.

```
$ kubectl get pods -n kube-system
```

- Bạn có thể xem các tùy chọn cho **kube-scheduler** trong tệp định nghĩa pod tại `/etc/kubernetes/manifests/kube-scheduler.yaml`.

```
$ cat /etc/kubernetes/manifests/kube-scheduler.yaml
```

- Bạn cũng có thể xem quy trình đang chạy và các tùy chọn hiệu quả bằng cách liệt kê quy trình trên node master và tìm kiếm **kube-scheduler**.

```
$ ps -aux | grep kube-scheduler
```

#### Tài liệu tham khảo K8s:

- [Tài liệu tham khảo dòng lệnh **kube-scheduler**](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-scheduler/)
- [Scheduling và Eviction với **kube-scheduler**](https://kubernetes.io/docs/concepts/scheduling-eviction/kube-scheduler/)
- [Các thành phần tổng quan](https://kubernetes.io/docs/concepts/overview/components/)
- [Cấu hình nhiều bộ lập lịch](https://kubernetes.io/docs/tasks/extend-kubernetes/configure-multiple-schedulers/)
