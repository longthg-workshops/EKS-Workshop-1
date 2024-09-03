---
title: "Triển khai môi trường lab trên Cloud9 (Đã ngừng hỗ trợ)"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: "<b> 2.1.1 </b>"
---

**_Lưu ý:_** _Hiện tại AWS sắp ngừng cung cấp Cloud9. Chúng tôi khuyến khích bạn dùng các giải pháp được nêu ở sau phần này._

#### **Hướng dẫn thiết lập môi trường chạy lab trong tài khoản AWS của bạn**

Bước đầu tiên là tạo một IDE với mẫu CloudFormation được cung cấp. Cách đơn giản nhất là mở giao diện CloudFormation theo các đường dẫn dưới đây:

Các region sau đã được kiểm tra và đảm bảo. Việc chạy các bài workshop tại các vùng địa lý khác có thể không được đảm bảo:

- us-west2
- eu-west-1
- ap-southeast-1

![EKS](/EKS-Workshop-1/images/2/1/1/00015.png?featherlight=false&width=90pc)

Một cách khác là mở **CloudShell** tại một trong các region trên và chạy các lệnh sau:

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

![EKS](/EKS-Workshop-1/images/2/1/1/00016.png?featherlight=false&width=90pc)

Bạn có thể đóng **CloudShell** ngay bây giờ, tất cả các lệnh tiếp theo sẽ được thực hiện trong phần terminal ở dưới cùng của **Cloud9 IDE**. **AWS CLI** đã được cài đặt sẵn và sẽ nhận các thông tin xác thực được gắn với **Cloud9 IDE**:

```bash test=false
$ aws sts get-caller-identity
```

Bước tiếp theo là tạo một cụm **EKS** để thực hiện các bài workshop. Nội dung này sẽ được trình bày tại [phần 2.2](../../2.2-cluster-creation/)
