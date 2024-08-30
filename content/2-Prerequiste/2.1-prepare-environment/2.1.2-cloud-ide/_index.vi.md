---
title: "Triển khai VSCode dưới dạng web host trên EC2 và CloudFront"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: "<b> 2.1.2 </b>"
---

**_Lưu ý:_** _Hiện tại AWS sắp ngừng cung cấp Cloud9. Chúng tôi hiện tại đang tìm các giải pháp thay thế sớm nhất có thể._

#### **Hướng dẫn thiết lập môi trường chạy lab trong tài khoản AWS của bạn**

Bước đầu tiên là tạo một IDE với mẫu CloudFormation được cung cấp. Mở **CloudShell** tại một trong các region _us-west-2_, _us-east-1_ hoặc _ap-southeast-1_ và chạy các lệnh sau:

```bash test=false
$ wget -q https://raw.githubusercontent.com/longthg-workshops/eks-workshop-v2-fork/cloud-ide/lab/cfn/ec2-workshop-cloud-ide-cfn.yaml -O eks-workshop-ide-cfn.yaml
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
```


{{% notice note %}}
Trong trường hợp CloudFormation báo lỗi không tạo được CloudFront Distribution do chưa xác thực tài khoản, bạn có thể yêu cầu nâng giới hạn tài nguyên CloudFront Distribution [tại đây](https://support.console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase).
![](/images//2/1/2/quota-failed-01.jpg?featherlight=false&width=30pc)
Tại mục _Service_, chọn "CloudFront Distribution".
![](/images//2/1/2/quota-inc-01.jpg?featherlight=false&width=90pc)
Tại mục _Request 1_, chọn _Quota_ là _Web Distributions per Account_ và đặt giá trị cao hơn giới hạn trước đó của bạn (nếu tài khoản của bạn mới được tạo và bạn chưa dùng CloudFront Distribution trên tài khoản, bạn có thể điền mọi giá trị dương)
![](/images//2/1/2/quota-inc-02.jpg?featherlight=false&width=90pc)
Tại mục _Case description_, chép mã lỗi chi tiết tại bước tạo CloudFront Distribution và dán vào khung, kèm miêu tả của bạn về lỗi gặp phải.
![](/images//2/1/2/quota-inc-03.jpg?featherlight=false&width=90pc)
Sau khi thực hiện xong, chọn phương thức liên hệ. Sau đó nhấn _Submit_ ở cuối trang.
![](/images//2/1/2/quota-inc-04.jpg?featherlight=false&width=90pc)
Hãy chờ một thời gian, theo dõi case và tích cực trao đổi để được hỗ trợ. Việc chờ xác thực tài khoản va nâng hạn mức
{{% /notice %}}


Mở URL này trong trình duyệt web để truy cập vào **IDE**.

![EKS](/images//2/1/2/vsc-web.png?featherlight=false&width=90pc)

Bạn có thể đóng **CloudShell** ngay bây giờ, tất cả các lệnh tiếp theo sẽ được thực hiện trong phần terminal ở dưới cùng của **Cloud9 IDE**. **AWS CLI** đã được cài đặt sẵn và sẽ nhận các thông tin xác thực được gắn với **Cloud9 IDE**:

```bash test=false
$ aws sts get-caller-identity
```

Bước tiếp theo là tạo một cụm **EKS** để thực hiện các bài workshop. Nội dung này sẽ được trình bày tại [phần 2.2](../../2.2-cluster-creation/)
