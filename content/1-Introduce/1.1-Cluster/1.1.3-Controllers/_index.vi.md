---
title: "Controllers"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: "<b> 1.1.3 </b>"
---

#### **Controllers**
- Trong lĩnh vực robot và tự động hóa, một **vòng lặp điều khiển** là vòng lặp không kết thúc giúp điều chỉnh trạng thái của một hệ thống.

- Dưới đây là một ví dụ về **vòng lặp điều khiển**: một bộ điều chỉnh nhiệt độ trong phòng.

- Khi bạn thiết lập nhiệt độ, điều đó nghĩa là bạn đang thông báo cho bộ điều chỉnh nhiệt về trạng thái mong muốn của bạn. Nhiệt độ thực tế của phòng là trạng thái hiện tại. - Bộ điều chỉnh nhiệt hoạt động để đưa trạng thái hiện tại gần hơn với trạng thái mong muốn, bằng cách bật hoặc tắt thiết bị.

- Trong **Kubernetes**, các **controller** là các **vòng lặp điều khiển** quan sát trạng thái của cluster của bạn, sau đó thực hiện hoặc yêu cầu các thay đổi khi cần thiết. Mỗi **controller** cố gắng di chuyển trạng thái hiện tại của cluster gần hơn với trạng thái mong muốn.

![Kubernetes](/images/4/0002.png?featherlight=false&width=60pc)

#### **Mô hình Controller**
- Một **controller** theo dõi ít nhất một loại tài nguyên Kubernetes. Những đối tượng này có một trường **spec** đại diện cho trạng thái mong muốn. Các **controller** cho tài nguyên đó chịu trách nhiệm làm cho trạng thái hiện tại gần hơn với trạng thái mong muốn.

- **Controller** có thể tự thực hiện hành động; thường thấy hơn, trong Kubernetes, một **controller** sẽ gửi tin nhắn tới máy chủ API mang lại hiệu ứng hữu ích. Bạn sẽ thấy ví dụ về điều này bên dưới.

#### **Điều khiển qua Máy chủ API**
- **Controller Job** là một ví dụ về **controller** tích hợp sẵn của Kubernetes. Các **controller** tích hợp sẵn quản lý trạng thái bằng cách tương tác với máy chủ API của cluster.

- **Job** là một tài nguyên Kubernetes chạy một Pod, hoặc có thể là nhiều Pods, để thực hiện một nhiệm vụ và sau đó dừng lại.

(Một khi được lên lịch, các đối tượng Pod trở thành phần của trạng thái mong muốn cho một kubelet).

- Khi **controller Job** thấy một nhiệm vụ mới, nó đảm bảo rằng, ở đâu đó trong cluster của bạn, các kubelet trên một nhóm Nodes đang chạy số lượng Pods đúng để hoàn thành công việc. **Controller Job** không chạy bất kỳ Pod hoặc container nào. Thay vào đó, **controller Job** yêu cầu máy chủ API tạo hoặc xóa Pods. Các thành phần khác trong bộ điều khiển thực hiện theo thông tin mới (có Pods mới cần được lên lịch và chạy), và cuối cùng công việc được hoàn thành.

- Sau khi bạn tạo một **Job** mới, trạng thái mong muốn là cho **Job** đó được hoàn thành. **Controller Job** làm cho trạng thái hiện tại của **Job** đó gần hơn với trạng thái mong muốn: tạo Pods thực hiện công việc bạn muốn cho **Job** đó, để **Job** gần hơn với việc hoàn thành.

- Các **controller** cũng cập nhật các đối tượng cấu hình cho chúng. Ví dụ: một khi công việc được hoàn thành cho một **Job**, **controller Job** cập nhật đối tượng **Job** đó để đánh dấu nó đã hoàn thành.

(Điều này giống như cách một số bộ điều chỉnh nhiệt tắt đèn để chỉ ra rằng phòng của bạn hiện ở nhiệt độ bạn đã thiết lập).

#### **Điều khiển Trực tiếp**
- Trái ngược với **Job**, một số **controller** cần thực hiện thay đổi đối với những thứ bên ngoài cluster của bạn.

- Ví dụ, nếu bạn sử dụng một **vòng lặp điều khiển** để đảm bảo có đủ Nodes trong cluster của bạn, thì **controller** đó cần một thứ gì đó bên ngoài cluster hiện tại để thiết lập Nodes mới khi cần thiết.

- Các **controller** tương tác với trạng thái bên ngoài tìm trạng thái mong muốn từ máy chủ API, sau đó trực tiếp giao tiếp với một hệ thống bên ngoài để đưa trạng thái hiện tại gần hơn.

(Thực tế có một **controller** điều chỉnh quy mô các nodes trong cluster của bạn theo chiều ngang).

- Điểm quan

 trọng ở đây là **controller** thực hiện một số thay đổi để đạt được trạng thái mong muốn, sau đó báo cáo trạng thái hiện tại trở lại máy chủ API của cluster. Các **vòng lặp điều khiển** khác có thể quan sát dữ liệu được báo cáo và thực hiện các hành động của riêng mình.

- Trong ví dụ về bộ điều chỉnh nhiệt, nếu phòng rất lạnh thì một **controller** khác cũng có thể bật máy sưởi chống đóng băng. Đối với các cluster Kubernetes, bộ điều khiển gián tiếp làm việc với các công cụ quản lý địa chỉ IP, dịch vụ lưu trữ, API của nhà cung cấp dịch vụ đám mây và các dịch vụ khác bằng cách mở rộng Kubernetes để triển khai điều đó.
