---
title: "Kustomize"
date: "`r Sys.Date()`"
weight: 4
chapter: false
pre: "<b> 4. </b>"
---

#### Kustomize

[Kustomize](https://kustomize.io/) cho phép bạn quản lý các tệp mẫu Kubernetes bằng cách sử dụng các tệp "kustomization" bằng cách khai báo. Nó cung cấp khả năng diễn đạt các tài nguyên Kubernetes "**cơ bản**" cho tài nguyên Kubernetes của bạn và sau đó áp dụng các thay đổi bằng cách sử dụng sự hợp thành, tùy chỉnh và dễ dàng thực hiện các thay đổi chéo qua nhiều tài nguyên.

Ví dụ, hãy xem tệp mẫu sau cho `checkout` Deployment:

**_~/environment/eks-workshop/base-application/checkout/deployment.yaml_**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: checkout
  labels:
    app.kubernetes.io/created-by: eks-workshop
    app.kubernetes.io/type: app
spec:
  replicas: 1
  selector:
    matchLabels:
      app.kubernetes.io/name: checkout
      app.kubernetes.io/instance: checkout
      app.kubernetes.io/component: service
  template:
    metadata:
      annotations:
        prometheus.io/path: /metrics
        prometheus.io/port: "8080"
        prometheus.io/scrape: "true"
      labels:
        app.kubernetes.io/name: checkout
        app.kubernetes.io/instance: checkout
        app.kubernetes.io/component: service
        app.kubernetes.io/created-by: eks-workshop
    spec:
      serviceAccountName: checkout
      securityContext:
        fsGroup: 1000
      containers:
        - name: checkout
          envFrom:
            - configMapRef:
                name: checkout
          securityContext:
            capabilities:
              drop:
                - ALL
            readOnlyRootFilesystem: true
          image: "public.ecr.aws/aws-containers/retail-store-sample-checkout:0.4.0"
          imagePullPolicy: IfNotPresent
          ports:
            - name: http
              containerPort: 8080
              protocol: TCP
          livenessProbe:
            httpGet:
              path: /health
              port: 8080
            initialDelaySeconds: 30
            periodSeconds: 3
          resources:
            limits:
              memory: 512Mi
            requests:
              cpu: 250m
              memory: 512Mi
          volumeMounts:
            - mountPath: /tmp
              name: tmp-volume
      volumes:
        - name: tmp-volume
          emptyDir:
            medium: Memory
```

Tệp này đã được áp dụng trong bài lab [Getting Started](../../getting-started) trước đó, nhưng giả sử chúng ta muốn mở rộng thành phần này theo chiều ngang bằng cách cập nhật trường `replicas` sử dụng Kustomize. Thay vì cập nhật tệp YAML này thủ công, chúng ta sẽ sử dụng Kustomize để cập nhật trường `spec/replicas` từ 1 thành 3.

Để làm điều này, chúng ta sẽ áp dụng kustomization sau.

* Tab đầu tiên hiển thị kustomization chúng ta đang áp dụng
* Tab thứ hai hiển thị một bản xem trước về cách tệp `Deployment/checkout` được cập nhật sau khi kustomization được áp dụng
* Cuối cùng, tab thứ ba chỉ hiển thị sự khác biệt của những thay đổi đã xảy ra

**_modules/introduction/kustomize/deployment.yaml_**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: checkout
spec:
  replicas: 3
```
**_Deployment/checkout_**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app.kubernetes.io/created-by: eks-workshop
    app.kubernetes.io/type: app
  name: checkout
  namespace: checkout
spec:
  replicas: 3
  selector:
    matchLabels:
      app.kubernetes.io/component: service
      app.kubernetes.io/instance: checkout
      app.kubernetes.io/name: checkout
  template:
    metadata:
      annotations:
        prometheus.io/path: /metrics
        prometheus.io/port: "8080"
        prometheus.io/scrape: "true"
      labels:
        app.kubernetes.io/component: service
        app.kubernetes.io/created-by: eks-workshop
        app.kubernetes.io/instance: checkout
        app.kubernetes.io/name: checkout
    spec:
      containers:
        - envFrom:
            - configMapRef:
                name: checkout
          image: public.ecr.aws/aws-containers/retail-store-sample-checkout:0.4.0
          imagePullPolicy: IfNotPresent
          livenessProbe:
            httpGet:
              path: /health
              port: 8080
            initialDelaySeconds: 30
            periodSeconds: 3
          name: checkout
          ports:
            - containerPort: 8080
              name: http
              protocol: TCP
          resources:
            limits:
              memory: 512Mi
            requests:
              cpu: 250m
              memory: 512Mi
          securityContext:
            capabilities:
              drop:
                - ALL
            readOnlyRootFilesystem: true
          volumeMounts:
            - mountPath: /tmp
              name: tmp-volume
      securityContext:
        fsGroup: 1000
      serviceAccountName: checkout
      volumes:
        - emptyDir:
            medium: Memory
          name: tmp-volume
```

Bạn có thể tạo ra YAML Kubernetes cuối cùng áp dụng kustomization này bằng lệnh `kubectl kustomize`, mà gọi `kustomize` được gói kèm với CLI `kubectl`:

```bash
$ kubectl kustomize ~/environment/eks-workshop/modules/introduction/kustomize
```

Điều này sẽ tạo ra nhiều tệp YAML, đại diện cho các tài nguyên cuối cùng bạn có thể áp dụng trực tiếp vào Kubernetes. Hãy minh họa điều này bằng cách chuyển dữ liệu đầu ra từ `kustomize` trực tiếp sang `kubectl apply`:

```bash
$ kubectl kustomize ~/environment/eks-workshop/modules/introduction/kustomize | kubectl apply -f -
namespace/checkout unchanged
serviceaccount/checkout unchanged
configmap/checkout unchanged
service/checkout unchanged
service/checkout-redis unchanged
deployment.apps/checkout configured
deployment.apps/checkout-redis unchanged
```

Bạn sẽ nhận thấy một số tài nguyên liên quan đến `checkout` "unchanged", với `deployment.apps/checkout` được "cấu hình". Điều này là có chủ ý — chúng ta chỉ muốn áp dụng các thay đổi vào `deployment` của `checkout`. Điều này xảy ra vì lệnh trước đó thực sự áp dụng hai tệp: `deployment.yaml` của Kustomize mà chúng ta đã thấy ở trên, cũng như tệp `kustomization.yaml` sau đây khớp với tất cả các tệp trong thư mục `~/environment/eks-workshop/base-application/checkout`. Trường `patches` chỉ định tệp cụ thể cần được vá:

**_~/environment/eks-workshop/modules/introduction/kustomize/kustomization.yaml_**
```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
  - ../../../base-application/checkout
patches:
  - path: deployment.yaml
```

Để kiểm tra xem số bản sao đã được cập nhật, chạy lệnh sau:

```bash
$ kubectl get pod -n checkout -l app.kubernetes.io/component=service
NAME                        READY   STATUS    RESTARTS   AGE
checkout-585c9b45c7-c456l   1/1     Running   0          2m12s
checkout-585c9b45c7-b2rrz   1/1     Running   0          2m12s
checkout-585c9b45c7-xmx2t   1/1     Running   0          40m
```

Thay vì sử dụng sự kết hợp của `kubectl kustomize` và `kubectl apply`, chúng ta có thể thực hiện cùng một việc với `kubectl apply -k <thư_mục_kustomization>` (chú ý cờ `-k` thay vì `-f`). Phương pháp này được sử dụng trong khóa học này để làm cho việc áp dụng các thay đổi vào các tệp mẫu dễ dàng hơn, trong khi rõ ràng hiển thị các thay đổi cần được áp dụng.

Hãy thử:

```bash
$ kubectl apply -k ~/environment/eks-workshop/modules/introduction/kustomize
```

Để đặt lại các tệp mẫu ứng dụng về trạng thái ban đầu của chúng, bạn có thể đơn giản áp dụng bộ tệp mẫu ban đầu:

```bash timeout=300 wait=30
$ kubectl apply -k ~/environment/eks-workshop/base-application
```

Mẫu khác bạn sẽ thấy được sử dụng trong một số bài lab có dạng như sau:

```bash
$ kubectl kustomize ~/environment/eks-workshop/base-application \
  | envsubst | kubectl apply -f-
```

Điều này sử dụng `envsubst` để thay thế các nơi giữ chỗ biến môi trường trong các tệp mẫu Kubernetes bằng các giá trị thực tế dựa trên môi trường cụ thể của bạn. Ví dụ trong một số tệp mẫu, chúng ta cần tham chiếu đến tên cụm EKS với `$EKS_CLUSTER_NAME` hoặc khu vực AWS với `$AWS_REGION`.

Bây giờ bạn đã hiểu cách Kustomize hoạt động, tiến hành [module Cơ bản](/docs/fundamentals).

Để tìm hiểu thêm về Kustomize, bạn có thể tham khảo [tài liệu chính thức của Kubernetes](https://kubernetes.io/docs/tasks/manage-kubernetes-objects/kustomization/).