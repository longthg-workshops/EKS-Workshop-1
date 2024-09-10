---
title: "Giao tiếp giữa các Node và Control Plane"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: "<b> 1.1.2 </b>"
---

Phần này liệt kê các đường giao tiếp giữa máy chủ API và cụm **Kubernetes**. Mục đích là để cho phép người dùng tùy chỉnh cài đặt của mình nhằm tăng cường cấu hình mạng, từ đó cụm có thể được vận hành trên một mạng không tin cậy (hoặc trên các IP công cộng hoàn toàn trên nhà cung cấp đám mây).

![Control Plane and Nodes](../../../../../../images/part1/1/2/0001.svg?featherlight=false&width=60pc)

### **Từ Node đến Control Plane**
- **Kubernetes** áp dụng mô hình API "hub-and-spoke". Tất cả việc sử dụng API từ các node (hoặc các pod được chúng chạy) đều kết thúc tại máy chủ API. Không có thành phần nào khác của control plane được thiết kế để tiết lộ dịch vụ từ xa. Máy chủ API được cấu hình để lắng nghe các kết nối từ xa trên cổng HTTPS an toàn (thông thường là 443) với một hoặc nhiều hình thức xác thực khách hàng được kích hoạt. Một hoặc nhiều hình thức ủy quyền nên được kích hoạt, đặc biệt nếu cho phép yêu cầu ẩn danh hoặc token tài khoản dịch vụ.

- Các node nên được cung cấp chứng chỉ root công khai cho cụm để chúng có thể kết nối một cách an toàn đến máy chủ API cùng với các thông tin đăng nhập khách hàng hợp lệ. Một cách tiếp cận tốt là thông tin đăng nhập khách hàng được cung cấp cho kubelet dưới dạng một chứng chỉ khách hàng. Xem khoản bootstrap TLS kubelet để tự động cung cấp chứng chỉ khách hàng cho kubelet.

- Các pod muốn kết nối đến máy chủ API có thể làm như vậy một cách an toàn bằng cách tận dụng một tài khoản dịch vụ sao cho **Kubernetes** sẽ tự động tiêm chứng chỉ root công khai và token bearer hợp lệ vào pod khi nó được khởi tạo. Dịch vụ **Kubernetes** (trong namespace mặc định) được cấu hình với một địa chỉ IP ảo được chuyển hướng (thông qua kube-proxy) đến điểm cuối HTTPS trên máy chủ API.

- Các thành phần của control plane cũng giao tiếp với máy chủ API qua cổng an toàn.

- Kết quả là, chế độ vận hành mặc định cho các kết nối từ các node và pod chạy trên các node đến control plane được bảo mật theo mặc định và có thể vận hành trên các mạng không tin cậy và/hoặc công cộng.

### **Từ Control Plane đến Node**
- Có hai con đường giao tiếp chính từ control plane (máy chủ API) đến các node. Đầu tiên là từ máy chủ API đến quy trình kubelet chạy trên mỗi node trong cụm. Thứ hai là từ máy chủ API đến bất kỳ node, pod, hoặc dịch vụ nào thông qua chức năng proxy của máy chủ API.

### **Máy chủ API đến kubelet**
Các kết nối từ máy chủ API đến kubelet được sử dụng cho:

- Lấy log cho các pod.
- Đính kèm (thường qua kubectl) vào các pod đang chạy.
- Cung cấp chức năng chuyển tiếp cổng của kubelet.
- Những kết nối này kết thúc tại điểm cuối HTTPS của kubelet. Theo mặc định, máy chủ API không xác minh chứng chỉ phục vụ của kubelet, điều này khiến kết nối dễ bị tấn công man-in-the-middle và không an toàn để vận hành trên các mạng không tin cậy và/hoặc công cộng.
- Để xác minh kết nối này, sử dụng cờ --kubelet-certificate-authority để cung cấp cho máy chủ API một gói chứng chỉ root để sử dụng để xác minh chứng chỉ phục vụ của kubelet.
- Nếu điều đó không khả thi, sử dụng SSH Tunnel giữa máy chủ API và kubelet nếu cần thiết để tránh kết nối qua một mạng không tin cậy hoặc công cộng.
- Cuối cùng, xác thực và/hoặc ủy quyền kubelet nên được kích hoạt để bảo mật API của kubelet.

### **Máy chủ API đến Node, Pod, và Dịch vụ**
- Các kết nối từ máy chủ API đến một node, pod, hoặc dịch vụ mặc định là các kết nối HTTP đơn giản và do đó không được xác thực hoặc mã hóa. Chúng có thể được vận hành qua một kết nối HTTPS an toàn bằng cách thêm tiền t

ố https: vào tên node, pod, hoặc dịch vụ trong URL API, nhưng chúng sẽ không xác minh chứng chỉ được cung cấp bởi điểm cuối HTTPS cũng như không cung cấp thông tin đăng nhập khách hàng. Vì vậy, mặc dù kết nối sẽ được mã hóa, nó không cung cấp bất kỳ bảo đảm nào về tính toàn vẹn. Những kết nối này hiện không an toàn để vận hành trên các mạng không tin cậy hoặc công cộng.
