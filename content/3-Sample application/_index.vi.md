---
title: "Ứng dụng mẫu"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: "<b> 3. </b>"
---

Chào mừng bạn đến với lab thực hành đầu tiên trong workshop **EKS**. Mục tiêu của bài tập này là làm quen với ứng dụng mẫu mà chúng ta sẽ sử dụng cho nhiều bài lab tiếp theo và qua đó đề cập đến một số khái niệm cơ bản liên quan đến triển khai workloads trên **EKS**. Chúng ta sẽ khám phá kiến trúc của ứng dụng và triển khai các thành phần vào cluster **EKS** của chúng ta.

Hãy triển khai workload đầu tiên của bạn vào cluster **EKS** trong môi trường lab của bạn và khám phá!

Trước khi chúng ta bắt đầu, chúng ta cần chạy lệnh sau để chuẩn bị môi trường **Cloud9** và cluster **EKS** của chúng ta:

```bash
$ prepare-environment introduction/getting-started
```

Lệnh này làm gì? Đối với lab này, nó sao chép kho Git Workshop **EKS** vào môi trường **Cloud9** để các tệp **Kubernetes Manifest** cần thiết tồn tại trên hệ thống tệp.

Bạn sẽ thấy trong các lab tiếp theo, chúng ta cũng sẽ chạy lệnh này, nơi nó sẽ thực hiện hai chức năng quan trọng bổ sung:

1. Thiết lập lại cluster **EKS** về trạng thái ban đầu của nó
2. Cài đặt các thành phần bổ sung cần thiết vào cluster cho bài lab tiếp theo

#### Sử Dụng Ứng Dụng Mẫu Trong AWS và Kubernetes

Đa số các lab trong workshop này sử dụng một ứng dụng mẫu chung để cung cấp các thành phần container thực tế mà chúng ta có thể làm việc trong suốt các bài tập. Ứng dụng mẫu mô hình một ứng dụng cửa hàng web đơn giản, nơi khách hàng có thể duyệt một danh mục, thêm các mặt hàng vào giỏ hàng của họ và hoàn tất một đơn hàng thông qua quá trình thanh toán.

![EKS](/EKS-Workshop-1/images/1/00017.png?featherlight=false&width=90pc)

Ứng dụng có một số thành phần và phụ thuộc:

![EKS](/EKS-Workshop-1/images/1/00018.png?featherlight=false&width=60pc)

| Thành Phần | Mô Tả                                                                 |
|-----------|-----------------------------------------------------------------------------|
| **UI**        | Cung cấp giao diện người dùng phía trước và tổng hợp cuộc gọi **API** đến các dịch vụ khác nhau. |
| **Catalog**   | **API** cho việc liệt kê và chi tiết sản phẩm                                                      |
| **Cart**   | **API** cho giỏ hàng mua sắm của khách hàng |
| **Checkout** | **API** để điều phối quá trình thanh toán                                   |
| **Orders** | **API** để nhận và xử lý đơn hàng của khách hàng                               |
| **Static assets**  | Cung cấp tài nguyên tĩnh như hình ảnh liên quan đến danh mục sản phẩm              |

Ban đầu, chúng ta sẽ triển khai ứng dụng một cách độc lập trong cụm **Amazon EKS**, mà không sử dụng bất kỳ dịch vụ **AWS** nào như cân bằng tải hoặc cơ sở dữ liệu được quản lý. Trong suốt các lab, chúng ta sẽ tận dụng các tính năng khác nhau của **EKS** để tận dụng các dịch vụ và tính năng của **AWS** rộng lớn hơn cho cửa hàng bán lẻ của chúng ta.

Bạn có thể tìm mã nguồn đầy đủ cho ứng dụng mẫu trên [GitHub](https://github.com/aws-containers/retail-store-sample-app).
