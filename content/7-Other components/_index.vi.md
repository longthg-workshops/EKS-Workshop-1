---
title: "Các thành phần khác"
date: "`r Sys.Date()`"
weight: 7
chapter: false
pre: "<b> 7. </b>"
---

Trong bài thực hành này, chúng ta sẽ triển khai phần còn lại của ứng dụng mẫu một cách hiệu quả bằng cách sử dụng sức mạnh của **Kustomize**. File kustomization sau đây cho thấy cách bạn có thể tham chiếu đến các **kustomizations** khác và triển khai nhiều thành phần cùng nhau:

```file
manifests/base-application/kustomization.yaml
```


Lưu ý rằng **API danh mục** nằm trong **kustomization** này, chúng ta đã triển khai nó chưa?

Bởi vì **Kubernetes** sử dụng cơ chế khai báo, chúng ta có thể áp dụng các **manifests** cho **API danh mục** một lần nữa và mong đợi rằng vì tất cả các tài nguyên đã được tạo ra, **Kubernetes** sẽ không thực hiện bất kỳ hành động nào.

Áp dụng **kustomization** này vào **cluster** của chúng ta để triển khai các thành phần còn lại:

```bash wait=10
$ kubectl apply -k ~/environment/eks-workshop/base-application
```

Sau khi hoàn thành điều này, chúng ta có thể sử dụng `kubectl wait` để đảm bảo rằng tất cả các thành phần đã được bắt đầu trước khi chúng ta tiếp tục:

```bash timeout=200
$ kubectl wait --for=condition=Ready --timeout=180s pods \
  -l app.kubernetes.io/created-by=eks-workshop -A
```

Bây giờ chúng ta sẽ có một **Namespace** cho mỗi thành phần ứng dụng của chúng ta:

```bash
$ kubectl get namespaces -l app.kubernetes.io/created-by=eks-workshop
NAME       STATUS   AGE
assets     Active   62s
carts      Active   62s
catalog    Active   7m17s
checkout   Active   62s
orders     Active   62s
other      Active   62s
rabbitmq   Active   62s
ui         Active   62s
```

Chúng ta cũng có thể thấy tất cả các **Deployments** được tạo ra cho các thành phần:

```bash
$ kubectl get deployment -l app.kubernetes.io/created-by=eks-workshop -A
NAMESPACE   NAME             READY   UP-TO-DATE   AVAILABLE   AGE
assets      assets           1/1     1            1           90s
carts       carts            1/1     1            1           90s
carts       carts-dynamodb   1/1     1            1           90s
catalog     catalog          1/1     1            1           7m46s
checkout    checkout         1/1     1            1           90s
checkout    checkout-redis   1/1     1            1           90s
orders      orders           1/1     1            1           90s
orders      orders-mysql     1/1     1            1           90s
ui          ui               1/1     1            1           90s
```

Ứng dụng mẫu hiện đã được triển khai và sẵn sàng cung cấp một nền tảng cho chúng ta để sử dụng trong các bài thực hành khác trong workshop này!
