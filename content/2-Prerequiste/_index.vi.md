---
title: "Các Bước Chuẩn Bị"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: "<b> 2. </b>"
---

#### **Các Bước Chuẩn Bị**

#### **Hướng dẫn thiết lập môi trường chạy lab trong tài khoản AWS của bạn**

Phần này trình bày cách thiết lập môi trường để chạy tất cả các workshop trong tài khoản AWS của bạn. Hướng dẫn này đã được kiểm tra ở các vùng địa lý (Region) AWS sau đây và không đảm bảo hoạt động ở các khu vực khác mà không cần chỉnh sửa:

- **`us-west-2`**
- **`eu-west-1`**

Bước đầu tiên là tạo một IDE với mẫu CloudFormation được cung cấp. Cách đơn giản nhất để làm điều này là sử dụng **AWS CloudShell** trong tài khoản mà bạn sẽ chạy các bài workshop. Mở **CloudShell** với liên kết bên dưới hoặc tuân theo [tài liệu này](https://docs.aws.amazon.com/cloudshell/latest/userguide/getting-started.html#launch-region-shell):

[https://console.aws.amazon.com/cloudshell/home](https://console.aws.amazon.com/cloudshell/home)


Nếu sử dụng liên kết ở trên, đảm bảo bảng điều khiển **AWS** đã mở ở vùng địa lý mà bạn muốn chạy các bài workshop.

![EKS](/EKS-Workshop-1/images/1/00015.png?featherlight=false&width=90pc)

Khi **CloudShell** đã tải xong, chạy các lệnh sau:

```bash test=false
$ wget -q https://raw.githubusercontent.com/aws-samples/eks-workshop-v2/stable/lab/cfn/eks-workshop-ide-cfn.yaml -O eks-workshop-ide-cfn.yaml
$ aws cloudformation deploy --stack-name eks-workshop-ide \
    --template-file ./eks-workshop-ide-cfn.yaml \
    --parameter-overrides RepositoryRef=stable \
    --capabilities CAPABILITY_NAMED_IAM
Waiting for changeset to be created...
Waiting for stack create/update to complete
Successfully created/updated stack - eks-workshop-ide
```

**CloudFormation Stack** sẽ mất khoảng 5 phút để triển khai, và khi hoàn tất, bạn có thể lấy URL cho **Cloud9 IDE** như sau:

```bash test=false
$ aws cloudformation describe-stacks --stack-name eks-workshop-ide \
    --query 'Stacks[0].Outputs[?OutputKey==`Cloud9Url`].OutputValue' --output text
https://us-west-2.console.aws.amazon.com/cloud9/ide/7b05513358534d11afeb7119845c5461?region=us-west-2
```

Mở URL này trong trình duyệt web để truy cập vào **IDE**.

![EKS](/EKS-Workshop-1/images/1/00016.png?featherlight=false&width=90pc)

Bạn có thể đóng **CloudShell** ngay bây giờ, tất cả các lệnh tiếp theo sẽ được thực hiện trong phần terminal ở dưới cùng của **Cloud9 IDE**. **AWS CLI** đã được cài đặt sẵn và sẽ nhận các thông tin xác thực được gắn với **Cloud9 IDE**:

```bash test=false
$ aws sts get-caller-identity
```

Bước tiếp theo là tạo một cụm **EKS** để thực hiện các bài workshop. Vui lòng tuân theo một trong các hướng dẫn dưới đây để cung cấp một cụm phù hợp với yêu cầu của các bài workshop này:
- **(Được khuyến nghị)** [eksctl](./using-eksctl.md)
- Terraform
- (Sắp ra mắt!) CDK
