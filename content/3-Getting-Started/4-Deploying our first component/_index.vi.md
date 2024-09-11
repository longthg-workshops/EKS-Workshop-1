---
title: "Triển khai thành phần đầu tiên"
date: "`r Sys.Date()`"
weight: 4
chapter: false
pre: "<b> 3.4 </b>"
---

#### Triển khai

#### Ứng dụng mẫu

Ứng dụng mẫu được tạo thành từ một tập hợp các tuyên bố **Kubernetes** được tổ chức một cách dễ dàng áp dụng bằng **Kustomize**. **Kustomize** là một công cụ mã nguồn mở cũng được cung cấp như một tính năng gốc của CLI `kubectl`. Hội thảo này sử dụng **Kustomize** để áp dụng các thay đổi vào các tuyên bố **Kubernetes**, giúp việc hiểu các thay đổi đối với các tệp tuyên bố mà không cần phải chỉnh sửa YAML thủ công. Khi chúng ta làm việc qua các module khác nhau của hội thảo này, chúng ta sẽ áp dụng các overlay và patch một cách từ từ bằng **Kustomize**.

Cách dễ nhất để duyệt các tuyên bố YAML cho ứng dụng mẫu và các module trong hội thảo này là sử dụng trình duyệt tệp trong **Cloud9**:

![EKS](../../../images/part6/00021.png?featherlight=false&width=60pc)

Mở rộng các mục `eks-workshop` và sau đó `base-application` sẽ cho phép bạn duyệt các tuyên bố tạo thành trạng thái ban đầu của ứng dụng mẫu:

![EKS](../../../images/part6/00022.png?featherlight=false&width=60pc)

Cấu trúc bao gồm một thư mục cho mỗi thành phần ứng dụng được mô tả trong phần **Ứng dụng mẫu**.

Thư mục `modules` chứa các bộ tuyên bố mà chúng ta sẽ áp dụng vào cụm trong các bài tập thực hành lab sau:

![EKS](../../../images/part6/00023.png?featherlight=false&width=60pc)

Trước khi làm bất kỳ điều gì, hãy kiểm tra các **Namespaces** hiện tại trong cụm **EKS** của chúng ta:

```bash
$ kubectl get namespaces
NAME                            STATUS   AGE
default                         Active   1h
kube-node-lease                 Active   1h
kube-public                     Active   1h
kube-system                     Active   1h
```

Tất cả các mục liệt kê là **Namespaces** cho các thành phần hệ thống đã được cài đặt sẵn cho chúng ta. Chúng ta sẽ bỏ qua chúng bằng cách sử dụng [nhãn **Kubernetes**](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) để lọc các **Namespaces** chỉ xuống các **Namespaces** mà chúng ta đã tạo:

```bash
$ kubectl get namespaces -l app.kubernetes.io/created-by=eks-workshop
No resources found
```

Điều đầu tiên chúng ta sẽ làm là triển khai thành phần **catalog** một mình. Các tuyên bố cho thành phần này có thể được tìm thấy trong `~/environment/eks-workshop/base-application/catalog`.

```bash
$ ls ~/environment/eks-workshop/base-application/catalog
configMap.yaml
deployment.yaml
kustomization.yaml
namespace.yaml
secrets.yaml
service-mysql.yaml
service.yaml
serviceAccount.yaml
statefulset-mysql.yaml
```

Các tuyên bố này bao gồm **Deployment** cho **API catalog**:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: catalog
  labels:
    app.kubernetes.io/created-by: eks-workshop
    app.kubernetes.io/type: app
spec:
  replicas: 1
  selector:
    matchLabels:
      app.kubernetes.io/name: catalog
      app.kubernetes.io/instance: catalog
      app.kubernetes.io/component: service
  template:
    metadata:
      annotations:
        prometheus.io/path: /metrics
        prometheus.io/port: "8080"
        prometheus.io/scrape: "true"
      labels:
        app.kubernetes.io/name: catalog
        app.kubernetes.io/instance: catalog
        app.kubernetes.io/component: service
        app.kubernetes.io/created-by: eks-workshop
    spec:
      serviceAccountName: catalog
      securityContext:
        fsGroup: 1000
      containers:
        - name: catalog
          env:
            - name: DB_USER
              valueFrom:
                secretKeyRef:
                  name: catalog-db
                  key: username
            - name: DB_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: catalog-db
                  key: password
          envFrom:
            - configMapRef:
                name: catalog
          securityContext:
            capabilities:
              drop:
                - ALL
            readOnlyRootFilesystem: true
            runAsNonRoot: true
            runAsUser: 1000
          image: "public.ecr.aws/aws-containers/retail-store-sample-catalog:0.4.0"
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
          readinessProbe:
            httpGet:
              path: /health
              port: 8080
            successThreshold: 3
            periodSeconds: 5
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

**Deployment** này diễn đạt trạng thái mong muốn của thành phần **API catalog**:

- Sử dụng hình ảnh container `public.ecr.aws/aws-containers/retail-store-sample-catalog`
- Chạy một bản sao duy nhất
- Tiếp tục container trên cổng 8080 có tên là `http`
- Chạy [probes/healthchecks](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/) chống lại đường dẫn `/health`
- [Requests](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/) một lượng CPU và bộ nhớ cụ thể để lập lịch **Kubernetes** có thể đặt nó trên một nút với đủ tài nguyên khả dụng
- Áp dụng các nhãn cho các **Pod** để các tài nguyên khác có thể tham chiếu đến chúng

Các tuyên bố cũng bao gồm **Service** được sử dụng bởi các thành phần khác để truy cập **API catalog**:

```file
apiVersion: v1
kind: Service
metadata:
  name: catalog
  labels:
    app.kubernetes.io/created-by: eks-workshop
spec:
  type: ClusterIP
  ports:
    - port: 80
      targetPort: http
      protocol: TCP
      name: http
  selector:
    app.kubernetes.io/name: catalog
    app.kubernetes.io/instance: catalog
    app.kubernetes.io/component: service
```

**Service** này:

- Lựa chọn các **Pod catalog** bằng cách sử dụng các nhãn phù hợp với những gì chúng ta đã diễn đạt trong **Deployment** ở trên
- Mở bản thân trên cổng 80
- Chỉ định cổng `http` được mở bởi **Deployment**, đồng nghĩa với cổng 8080

Hãy tạo thành phần **catalog**:

```bash
$ kubectl apply -k ~/environment/eks-workshop/base-application/catalog
namespace/catalog created
serviceaccount/catalog created
configmap/catalog created
secret/catalog-db created
service/catalog created
service/catalog-mysql created
deployment.apps/catalog created
statefulset.apps/catalog-mysql created
```

Bây giờ chúng ta sẽ thấy một **Namespace** mới:

```bash
$ kubectl get namespaces -l app.kubernetes.io/created-by=eks-workshop
NAME      STATUS   AGE
catalog   Active   15s
```

Chúng ta có thể xem các **Pod** đang chạy trong **namespace** này:

```bash
$ kubectl get pod -n catalog
NAME                       READY   STATUS    RESTARTS      AGE
catalog-846479dcdd-fznf5   1/1     Running   2 (43s ago)   46s
catalog-mysql-0            1/1     Running   0             46
```

Bạn đã hoàn thành việc triển khai thành phần **catalog** thành công.