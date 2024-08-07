---
title: "Helm"
date: "`r Sys.Date()`"
weight: 10
chapter: false
pre: "<b> 9. </b>"
---

Mặc dù chúng ta chủ yếu tương tác với tùy chỉnh trong bộ workshop này, sẽ có những tình huống Helm được sử dụng để cài đặt một số gói nhất định trong cụm EKS. Trong bài thực hành này, chúng tôi giới thiệu ngắn gọn về Helm cùng cách sử dụng nó để cài đặt một ứng dụng đóng gói sẵn.

### Chuẩn bị môi trường
Chạy lệnh sau để chuẩn bị môi trường cho lab:
```bash
prepare-environment introduction/helm
```
Helm là trình quản lý gói dành cho Kubernetes giúp xác định, cài đặt và nâng cấp các ứng dụng Kubernetes. Nó sử dụng định dạng đóng gói được gọi là "chart", chứa tất cả các định nghĩa tài nguyên Kubernetes cần thiết để chạy một ứng dụng. Helm đơn giản hóa việc triển khai và quản lý ứng dụng trên cụm Kubernetes.

### Helm CLI
Cung cấp CLI cho phép nhà phát triển quản lý, khởi tạo, phát triển các Charts, Config, Release, Repositores một cách tự động.
Để sử dụng Helm CLI kiểm tra phiên bản Helm nào đã được cài đặt, chạy lệnh sau:

```bash
$ helm version
```

### Helm Repo
Kho lưu trữ Helm là nơi tập trung các Helm Chart nhằm lưu trữ và quản lý.

Ở dưới đây là các lệnh để thêm, cập nhật và tìm kiếm kho Helm Bitnami. Đây là nơi lưu trữ một số ứng dụng/dịch vụ phổ biến trên Kubernetes:

```bash
$ helm repo add bitnami https://charts.bitnami.com/bitnami
$ helm repo update
$ helm search repo postgresql

NAME                    CHART VERSION   APP VERSION     DESCRIPTION
bitnami/postgresql      X.X.X           X.X.X           PostgreSQL (Postgres) is an open source object-...
[...]
```

### Cài đặt Helm chart
```bash
$ echo $NGINX_CHART_VERSION
$ helm install nginx bitnami/nginx \
  --version $NGINX_CHART_VERSION \
  --namespace nginx --create-namespace --wait
```
Các thành phần câu lệnh:

- Lệnh con `install` thực hiện công việc cài đặt Helm chart. Các Helm chart có thể được nâng cấp hoặc quay lại phiên bản cũ.
-  `nginx` là tên của bản triển khai trên cụm EKS của bạn.
-  `bitnami/nginx` chính là tên của chart `nginx` trên kho `bitnami`.
- Tham số `--version` xác định phiên bản của chart được cài đặt.
- Cờ `--create-namespace` sẽ tạo namespace mới với tên đã xác định bằng tham số `--namespace`.

### Liệt kê chart đã cài đặt
```bash
$ helm list -A

NAME 	 NAMESPACE  REVISION  UPDATED                                  STATUS    CHART         APP VERSION
nginx	 nginx      1         2024-06-11 03:58:39.862100855 +0000 UTC  deployed  nginx-X.X.X   X.X.X
```
Liệt kê các pod thuộc chart đã cài đặt:

```bash
$ kubectl get pod -n nginx
NAME                     READY   STATUS    RESTARTS   AGE
nginx-55fbd7f494-zplwx   1/1     Running   0          119s
```

### Cấu hình các giá trị trong chart
Tại thư mục chính của chart, có một tệp với tên `value.yaml`. Tệp này được dùng để xác định các giá trị và truyền vào chart. Để thay thế các giá trị mặc định, ta có thể dùng một trong các cách sau:

1. Tạo file yaml và truyền giá trị vào đó qua các tham số `-f` hoặc `--values`.
2. Sử dụng cờ `--set` với cặp khoá - giá trị `key=value`.

Chúng ta kết hợp cả hai bằng các tạo tệp YAML sau:
```file
~/environment/eks-workshop/modules/introduction/helm/values.yaml
```
```yaml
podLabels:
  team: team1
  costCenter: org1

resources:
  requests:
    cpu: 250m
    memory: 256Mi
```

Ta triển khai các 3 bản sao bằng lệnh sau:
```bash
helm upgrade --install nginx bitnami/nginx \
  --version $NGINX_CHART_VERSION \
  --namespace nginx --create-namespace --wait \
  --set replicaCount=3 \
  --values ~/environment/eks-workshop/modules/introduction/helm/values.yaml
```

Kiểm tra lịch sử triển khai Helm chart:
```bash
$ helm history nginx -n nginx

REVISION  UPDATED                   STATUS      CHART        APP VERSION  DESCRIPTION
1         Tue Jun 11 03:58:39 2024  superseded  nginx-X.X.X  X.X.X       Install complete
2         Tue Jun 11 04:13:53 2024  deployed    nginx-X.X.X  X.X.X       Upgrade complete
```

### Gỡ cài đặt Helm chart
Để gỡ Helm chart nginx trong namespace nginx, chạy lệnh sau:

```bash
$ helm uninstall nginx --namespace nginx --wait
```