---
title: "Sử dụng Terraform"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: "<b> 2.2 </b>"
---

#### Xây dựng một cụm cho các bài thực hành Lab sử dụng [Hashicorp Terraform](https://developer.hashicorp.com/terraform).

Đây nhằm mục đích phục vụ cho những người học đã quen với việc làm việc với hạ tầng mã nguồn mở của Terraform.

CLI **terraform** đã được cài đặt sẵn trong Môi trường Amazon Cloud9 của bạn, vì vậy chúng ta có thể ngay lập tức tạo ra cụm. Hãy xem qua các tập tin cấu hình Terraform chính sẽ được sử dụng để xây dựng cụm và hạ tầng hỗ trợ của nó.

#### Hiểu các tập tin cấu hình Terraform

Tập tin `providers.tf` cấu hình các nhà cung cấp Terraform mà sẽ cần để xây dựng hạ tầng. Trong trường hợp này, của chúng ta sử dụng các nhà cung cấp **aws**, **kubernetes** và **helm**:

```file hidePath=true
manifests/../cluster/terraform/providers.tf
```

Tập tin `main.tf` thiết lập một số nguồn dữ liệu Terraform để chúng ta có thể lấy thông tin tài khoản AWS và region hiện đang được sử dụng, cũng như một số thẻ mặc định:

```file hidePath=true
manifests/../cluster/terraform/main.tf
```

Cấu hình `vpc.tf` sẽ đảm bảo hạ tầng VPC của chúng ta được tạo ra:

```file hidePath=true
manifests/../cluster/terraform/vpc.tf
```

Cuối cùng, tập tin `eks.tf` chỉ định cấu hình cụm EKS của chúng tôi, bao gồm một Nhóm Node Quản lý:

```file hidePath=true
manifests/../cluster/terraform/eks.tf
```

#### Tạo môi trường workshop với Terraform

Đối với cấu hình đã cho, **terraform** sẽ tạo ra Môi trường Workshop với các bước sau:
- Tạo một VPC qua ba AZ
- Tạo ra một cụm EKS
- Tạo một nhà cung cấp IAM OIDC
- Thêm một nhóm node quản lý có tên là `default`
- Cấu hình VPC CNI để sử dụng ủy quyền tiền tố

Tải các tập tin Terraform về:

```bash
$ mkdir -p ~/environment/terraform; cd ~/environment/terraform
$ curl --remote-name-all https://raw.githubusercontent.com/aws-samples/eks-workshop-v2/stable/cluster/terraform/{main.tf,variables.tf,providers.tf,vpc.tf,eks.tf}
```

Chạy các lệnh Terraform sau để triển khai môi trường workshop của bạn.

```bash
$ terraform init
$ terraform apply -var="cluster_name=$EKS_CLUSTER_NAME" -auto-approve
```

Thường mất khoảng 20-25 phút để hoàn thành. Sau khi cụm được tạo ra, chạy lệnh này để sử dụng cụm cho các bài thực hành Lab:

```bash
$ use-cluster $EKS_CLUSTER_NAME
```

#### Bước Tiếp Theo

Bây giờ cụm đã sẵn sàng, hãy đi đến [Bắt Đầu](/docs/introduction/getting-started) hoặc nhảy qua bất kỳ mô-đun nào trong workshop với thanh điều hướng hàng đầu. Khi bạn hoàn thành với workshop, làm theo các bước dưới đây để dọn dẹp môi trường của bạn.

#### Dọn dẹp

Phần sau đây sẽ hướng dẫn cách dọn dẹp các tài nguyên sau khi bạn đã hoàn thành các bài thực hành mong muốn. Những bước này sẽ xóa hết tất cả hạ tầng được cung cấp.

Trước khi xóa môi trường Cloud9, chúng ta cần dọn dẹp cụm mà chúng ta đã thiết lập ở trên.

Đầu tiên, sử dụng `delete-environment` để đảm bảo rằng ứng dụng mẫu và bất kỳ hạ tầng Lab nào còn sót lại đều được loại bỏ:

```bash
$ delete-environment
```

Tiếp theo, xóa cụm với **terraform**:

```bash
$ cd ~/environment/terraform
$ terraform destroy -var="cluster_name=$EKS_CLUSTER_NAME" -auto-approve
```
