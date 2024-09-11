---
title: "Bắt đầu"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: "<b> 3. </b>"
---

Chào mừng đến với bài thực hành đầu tiên trong bộ Workshop EKS. Mục tiêu của bài tập này là làm quen với ứng dụng mẫu để sử dụng cho nhiều bài tập sắp tới. Trong quá trình đó, chúng ta sẽ đề cập đến một số khái niệm cơ bản liên quan đến việc triển khai khối lượng công việc cho EKS. Chúng ta sẽ tìm hiểu kiến ​​trúc của ứng dụng và triển khai các thành phần cho cụm EKS của mình.

Hãy triển khai khối lượng công việc đầu tiên của bạn cho cụm EKS trong môi trường bài thực hành của bạn và cùng tìm hiểu !

Trước khi bắt đầu, chúng ta cần chạy lệnh sau để chuẩn bị môi trường IDE và cụm EKS của mình:

```bash
$ prepare-environment introduction/getting-started
```

Lệnh này có tác dụng gì? Đối với bài thực hành này, nó đang sao chép kho lưu trữ Git của EKS Workshop vào môi trường IDE để các tệp kê khai Kubernetes mà chúng ta cần có trên hệ thống tệp.

Bạn sẽ thấy trong các bài thực hành tiếp theo, chúng ta cũng sẽ chạy lệnh này, trong đó nó sẽ thực hiện hai chức năng bổ sung quan trọng:

1. Đặt lại cụm EKS về trạng thái ban đầu
2. Cài đặt bất kỳ thành phần bổ sung nào cần thiết vào cụm cho bài tập bài thực hành sắp tới