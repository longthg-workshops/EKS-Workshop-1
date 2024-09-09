---
title: "Nodes"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: "<b> 1.1.1 </b>"
---

**Kubernetes** chạy các tác vụ của bạn bằng cách đặt các container vào trong **Pods** để chạy trên **Nodes**. Một node có thể là máy ảo hoặc máy vật lý, tùy thuộc vào cụm. Mỗi node được quản lý bởi bộ điều khiển và chứa các dịch vụ cần thiết để chạy **Pods**.

![Kubernetes](../../../../../../images/part1/1/1/0001.png?featherlight=false&width=40pc)

Thông thường, bạn sẽ có nhiều **nodes** trong một cụm; trong một môi trường học tập hoặc môi trường có hạn chế về tài nguyên, bạn có thể chỉ có một **node**.

Các thành phần trên một **node** bao gồm **kubelet**, một runtime container, và **kube-proxy**.

### Quản lý

Có hai cách chính để thêm Node vào máy chủ API:

1. Kubelet trên một node tự đăng ký với Control Plane
2. Bạn (hoặc người dùng khác) tự thêm đối tượng Node

Sau khi tạo đối tượng Node hoặc kubelet trên một node tự đăng ký, Control Plane sẽ kiểm tra xem đối tượng Node mới có hợp lệ hay không. Ví dụ: nếu bạn thử tạo Node từ tệp kê khai JSON sau:

```json
{
  "kind": "Node",
  "apiVersion": "v1",
  "metadata": {
    "name": "10.240.79.157",
    "labels": {
      "name": "my-first-k8s-node"
    }
  }
}
```
Kubernetes tạo một đối tượng Node nội bộ (đại diện). Kubernetes kiểm tra xem kubelet đã đăng ký với API Server khớp với trường metadata.name của Node hay chưa. Nếu node khỏe mạnh (tức là tất cả các dịch vụ cần thiết đều đang chạy), thì node đó đủ điều kiện để chạy Pod. Nếu không, node đó sẽ bị bỏ qua đối với bất kỳ hoạt động cụm nào cho đến khi node đó khỏe mạnh.

### Tính độc nhất của tên node
Một Node được xác định bằng tên. Hai Node không thể cùng lúc mang cùng tên. Kubernetes cũng giả định rằng một tài nguyên với một tên gọi là cùng một đối tượng. Trong trường hợp của một Node, ta ngầm định rằng một đại diện sử dụng cùng tên sẽ có cùng trạng thái (ví dụ: cài đặt mạng, nội dung đĩa root) và các thuộc tính như nhãn node. Điều này có thể dẫn đến sự không nhất quán nếu một thể hiện được sửa đổi mà không thay đổi tên của nó. Nếu Node cần được thay thế hoặc áp dụng nhiều cập nhật, đối tượng Node hiện có cần được xóa khỏi máy chủ API trước và thêm lại sau khi cập nhật.

### Trạng thái node
Trạng thái của một Node chứa các thông tin sau:

- Địa chỉ

- Điều kiện/Tình trạng

- Dung lượng và khả năng phân bổ

- Thông tin

Bạn có thể sử dụng kubectl để xem trạng thái của một Node và các chi tiết khác:

```bash
kubectl describe node <insert-node-name-here>
```

### Theo dõi lượng tài nguyên
Đối tượng Node theo dõi thông tin về lượng tài nguyên của Node: ví dụ, dung lượng bộ nhớ khả dụng và số lượng CPU. Các Node tự đăng ký sẽ báo cáo dung lượng của chúng trong quá trình đăng ký. Nếu bạn thêm Node theo cách thủ công, thì bạn cần đặt thông tin dung lượng của node khi thêm node đó.

Trình lên lịch Kubernetes đảm bảo rằng có đủ tài nguyên cho tất cả các Pod trên Node. Trình lên lịch kiểm tra xem tổng các yêu cầu của container trên node có lớn hơn dung lượng của node hay không. Tổng các yêu cầu đó bao gồm tất cả các container do kubelet quản lý, nhưng không bao gồm bất kỳ container nào được khởi chạy trực tiếp bởi thời gian chạy container và cũng không bao gồm bất kỳ quy trình nào chạy bên ngoài tầm kiểm soát của kubelet.
