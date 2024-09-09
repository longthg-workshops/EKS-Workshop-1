---
title: "Kiến trúc Cluster"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: "<b> 1.1 </b>"
---

#### **Kiến trúc của một cluster Kubernetes**

Kiến trúc của một cluster Kubernetes bao gồm các thành phần chính sau:

![Kubernetes Arch](../../../../images/part1/1/0002.png?featherlight=false&width=60pc)

1. **Master Node**: Master node quản lý và điều khiển toàn bộ cluster. Các thành phần chính trên master node bao gồm:

   - **API Server**: API server là giao diện chính để tương tác với cluster. Tất cả các yêu cầu API từ các thành phần khác đều được xử lý thông qua API server.
   
   - **Scheduler**: Scheduler quyết định nơi chạy các pod mới dựa trên yêu cầu tài nguyên, các ràng buộc và chính sách đã được định nghĩa.
   
   - **Controller Manager**: Controller manager chịu trách nhiệm kiểm soát trạng thái của các đối tượng trong cluster (ví dụ: pods, services, replication controllers).
   
   - **etcd**: etcd là một cơ sở dữ liệu phân tán dùng để lưu trữ trạng thái của toàn bộ cluster.

2. **Worker Node**: Các worker node là nơi chứa các container và nơi mà các ứng dụng thực sự chạy. Mỗi worker node có các thành phần sau:

   - **Kubelet**: Kubelet là một agent chạy trên mỗi node và quản lý việc chạy các container trên node đó.
   
   - **Kube-proxy**: Kube-proxy quản lý việc giao tiếp mạng giữa các pod và các dịch vụ khác trong cluster.
   
   - **Container Runtime**: Container runtime (như Docker hoặc containerd) là phần mềm chịu trách nhiệm quản lý các container.

3. **Pods**: Pod là đơn vị nhỏ nhất trong Kubernetes, mỗi pod chứa một hoặc nhiều container. Các container trong cùng một pod chia sẻ cùng một không gian mạng và lưu trữ.

4. **Services**: Services định tuyến yêu cầu đến các pod. Một service đại diện cho một nhóm các pod và cung cấp cách truy cập đồng nhất đến chúng.

5. **Volumes**: Volumes là cách để lưu trữ dữ liệu dạng trên đĩa mà các container có thể chia sẻ và truy cập.

6. **Namespace**: Namespace cho phép chia cluster thành các phần nhỏ hơn, mỗi phần có thể có các tài nguyên riêng biệt và được cấu hình riêng.

7. **Ingress Controller**: Ingress controller quản lý việc điều hướng yêu cầu HTTP/HTTPS đến các dịch vụ trong cluster dựa trên các quy tắc cấu hình.

8. **Storage Classes**: Storage classes định nghĩa các loại lưu trữ khác nhau các persistent volume có thể yêu cầu.

Tất cả các thành phần này làm việc cùng nhau để tạo ra một môi trường chạy ứng dụng linh hoạt và mạnh mẽ trên Kubernetes cluster.
