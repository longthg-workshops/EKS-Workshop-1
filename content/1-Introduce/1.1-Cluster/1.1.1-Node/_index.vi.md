---
title: "Nodes"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: "<b> 1.1.1 </b>"
---

#### Nodes


- **Kubernetes** chạy các tác vụ của bạn bằng cách đặt các container vào trong **Pods** để chạy trên **Nodes**. Một node có thể là máy ảo hoặc máy vật lý, tùy thuộc vào cụm. Mỗi node được quản lý bởi bộ điều khiển và chứa các dịch vụ cần thiết để chạy **Pods**.

![Kubernetes](/EKS-Workshop-1/images/part1/1/1/0001.png?featherlight=false&width=40pc)

- Thông thường, bạn sẽ có nhiều **nodes** trong một cụm; trong một môi trường học tập hoặc môi trường có hạn chế về tài nguyên, bạn có thể chỉ có một **node**.

- Các thành phần trên một **node** bao gồm **kubelet**, một runtime container, và **kube-proxy**.
