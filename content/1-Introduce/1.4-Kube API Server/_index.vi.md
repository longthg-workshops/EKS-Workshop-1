---
title: "Kube API Server"
date: "`r Sys.Date()`"
weight: 4
chapter: false
pre: "<b> 1.4 </b>"
---

**Kube-apiserver** là thành phần chính trong Kubernetes. **Kube-apiserver** chịu trách nhiệm xác thực, kiểm tra yêu cầu, truy xuất và cập nhật dữ liệu trong cửa hàng khóa-giá trị **ETCD**. Thực tế, **kube-apiserver** là thành phần duy nhất tương tác trực tiếp với cơ sở dữ liệu **etcd**. Các thành phần khác như **kube-scheduler**, **kube-controller-manager** và **kubelet** sử dụng **API-Server** để cập nhật trong cụm ở các lĩnh vực tương ứng của họ.

![Kubernetes API Srv](/EKS-Workshop-1/images/part1/4/0005.png?featherlight=false&width=60pc)

### **Cài đặt kube-apiserver**

Nếu bạn đang khởi động **kube-apiserver** sử dụng công cụ **kubeadm**, thì bạn không cần phải biết điều này, nhưng nếu bạn đang thiết lập theo cách thủ công, thì **kube-apiserver** có sẵn dưới dạng một tệp nhị phân trên trang phát hành Kubernetes. Ví dụ: Bạn có thể tải xuống nhị phân **kube-apiserver** v1.13.0 tại đây:

```
$ wget https://storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kube-apiserver
```

### **Xem kube-apiserver - Kubeadm**

- **Kubeadm** triển khai **kube-apiserver** dưới dạng một pod trong namespace **kube-system** trên node master.

```
$ kubectl get pods -n kube-system
```

### **Xem các tùy chọn của kube-apiserver - Kubeadm**

- Bạn có thể xem các tùy chọn trong tệp định nghĩa pod tại `/etc/kubernetes/manifests/kube-apiserver.yaml`

```
$ cat /etc/kubernetes/manifests/kube-apiserver.yaml
```

### **Xem các tùy chọn của kube-apiserver - Cách thủ công**

- Trong một cài đặt không sử dụng **kubeadm**, bạn có thể kiểm tra các tùy chọn bằng cách xem dịch vụ `kube-apiserver.service`

```
$ cat /etc/systemd/system/kube-apiserver.service
```

- Bạn cũng có thể xem quá trình đang chạy và các tùy chọn hiệu quả bằng cách liệt kê quá trình trên node master và tìm kiếm `kube-apiserver`

```
$ ps -aux | grep kube-apiserver
```

### **Tài liệu tham khảo K8s:**

- [Kube API Server Command Line Tools Reference](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-apiserver/)
- [Kubernetes Components Overview](https://kubernetes.io/docs/concepts/overview/components/)
- [Kubernetes API Overview](https://kubernetes.io/docs/concepts/overview/kubernetes-api/)
- [Accessing Kubernetes Cluster](https://kubernetes.io/docs/tasks/access-application-cluster/access-cluster/)
- [Accessing Kubernetes Cluster API](https://kubernetes.io/docs/tasks/administer-cluster/access-cluster-api/)
