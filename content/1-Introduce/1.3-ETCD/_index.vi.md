---
title: "ETCD"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: "<b> 1.3 </b>"
---

- **ETCD** là một hệ thống lưu trữ **key-value** phân tán, đáng tin cậy, đơn giản, an toàn và nhanh chóng.

![Kubernetes](../../../../images/part1/3/0004.png?featherlight=false&width=60pc)

### **Key-Value Store** là gì?
- Từ trước đến nay, **Key-Value Store** là cơ sở dữ liệu được lưu trữ dưới dạng bảng, bạn có thể đã nghe về **SQL** hoặc cơ sở dữ liệu quan hệ. Chúng lưu trữ dữ liệu theo dòng và cột.

- Một **Key-Value Store** lưu trữ thông tin dưới dạng **Key** và **Value**.

### Cài đặt ETCD

- Việc cài đặt và bắt đầu với **ETCD** rất dễ dàng.

- Tải xuống file thực thi phù hợp với hệ điều hành của bạn từ trang phát hành trên Github ([ETCD Releases](https://github.com/etcd-io/etcd/releases))

- Ví dụ: Để tải xuống **ETCD v3.5.6**, chạy lệnh curl sau:

```bash
$ curl -LO https://github.com/etcd-io/etcd/releases/download/v3.5.6/etcd-v3.5.6-linux-amd64.tar.gz
```

- Giải nén nó.

```bash
$ tar xvzf etcd-v3.5.6-linux-amd64.tar.gz
```

- Chạy dịch vụ **ETCD**.

```bash
$ ./etcd
```

- Khi khởi động **ETCD**, mặc định nó sẽ tiếp nhận yêu cầu trên cổng 2379.

- Client mặc định đi kèm với **ETCD** là **etcdctl**. Bạn có thể sử dụng nó để lưu trữ và truy xuất cặp **key-value**.

- Cú pháp: Để lưu một cặp **Key-Value**.

```bash
$ ./etcdctl put key1 value1
```

- Cú pháp: Để truy xuất dữ liệu đã lưu.

```bash
$ ./etcdctl get key1
```

- Cú pháp: Để xem thêm các lệnh. Chạy **etcdctl** mà không cần bất kỳ đối số nào.

```bash
$ ./etcdctl
```

### Bộ lưu Dữ Liệu ETCD

Bộ lưu Dữ Liệu **ETCD** lưu trữ thông tin về cluster như **Nodes**, **PODS**, **Configs**, **Secrets**, **Accounts**, **Roles**, **Bindings** và các thông tin khác. Mọi thông tin bạn thấy khi chạy lệnh `kubectl get` đều đến từ Máy chủ **ETCD**.

### Cài đặt - Thủ công

Nếu bạn thiết lập cluster từ số không, bạn sẽ phải triển khai **ETCD** bằng cách tự tải xuống các tệp thực thi **ETCD**.

Cài đặt các tệp thực thi và cấu hình **ETCD** như một dịch vụ trên node master của bạn.

```bash
$ wget -q --https-only "https://github.com/etcd-io/etcd/releases/download/v3.3.11/etcd-v3.3.11-linux-amd64.tar.gz"
etcd
```

### Cài đặt - Kubeadm

- Nếu bạn thiết lập cluster sử dụng **kubeadm**, thì **kubeadm** sẽ triển khai máy chủ **etcd** cho bạn dưới dạng một pod trong không gian tên **kube-system**.

```bash
$ kubectl get pods -n kube-system
etcd1
```

### Tìm hiểu về ETCD

- Để liệt kê tất cả các khóa được lưu trữ bởi **kubernetes**, chạy lệnh dưới đây:

```bash
$ kubectl exec etcd-master -n kube-system -- sh -c "ETCDCTL_API=3 etcdctl --cert=/etc/kubernetes/pki/etcd/server.crt --key=/etc/kubernetes/pki/etcd/server.key --cacert=/etc/kubernetes/pki/etcd/ca.crt get / --prefix --keys-only"
```

- **Kubernetes** lưu trữ dữ liệu trong một cấu trúc thư mục cụ thể, thư mục gốc là **registry** và dưới đó bạn có các cấu trúc **kubernetes** khác như **minions**, **nodes**, **pods**, **replicasets**, **deployments**, **roles**, **secrets** và các thông tin khác.

### ETCD trong Môi Trường HA (High Availability)

- Trong một môi trường có khả năng cao sẵn sàng, cluster của bạn sẽ có nhiều node master, sẽ có nhiều thể hiện **ETCD** được phân bố trên các node master này.

- Đảm bảo các thể hiện **ETCD** biết về nhau bằng cách thiết lập tham số đúng trong cấu hình **etcd.service**. Tùy chọn **--initial-cluster** nơi bạn cần chỉ định các thể hiện khác nhau của dịch vụ **etcd**.


### Tài liệu tham khảo
- https://kubernetes.io/docs/concepts/overview/components/
- https://etcd.io/docs/
- https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/
- https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/setup-ha-etcd-with-kubeadm/
- https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/high-availability/#stacked-control-plane-and-etcd-nodes
- https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/high-availability/#external-etcd-nodes