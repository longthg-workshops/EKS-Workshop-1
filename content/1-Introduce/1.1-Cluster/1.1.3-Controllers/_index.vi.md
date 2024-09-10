---
title: "Controllers"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: "<b> 1.1.3 </b>"
---

Trong lĩnh vực robot và tự động hóa, một **vòng lặp điều khiển** là vòng lặp không kết thúc giúp điều chỉnh trạng thái của một hệ thống.

Dưới đây là một ví dụ về **vòng lặp điều khiển**: một bộ điều chỉnh nhiệt độ trong phòng.

- Khi bạn thiết lập nhiệt độ, điều đó nghĩa là bạn đang thông báo cho bộ điều chỉnh nhiệt về trạng thái mong muốn của bạn. Nhiệt độ thực tế của phòng là trạng thái hiện tại. - Bộ điều chỉnh nhiệt hoạt động để đưa trạng thái hiện tại gần hơn với trạng thái mong muốn, bằng cách bật hoặc tắt thiết bị.

Trong **Kubernetes**, các **controller** là các **vòng lặp điều khiển** quan sát trạng thái của cluster của bạn, sau đó thực hiện hoặc yêu cầu các thay đổi khi cần thiết. Mỗi **controller** cố gắng di chuyển trạng thái hiện tại của cluster gần hơn với trạng thái mong muốn.

![Kubernetes Controller](../../../../images/part1/1/3/0002.png?featherlight=false&width=60pc)

### **Mô hình Controller**
- Một **controller** theo dõi ít nhất một loại tài nguyên Kubernetes. Những đối tượng này có một trường **spec** đại diện cho trạng thái mong muốn. Các **controller** cho tài nguyên đó chịu trách nhiệm làm cho trạng thái hiện tại gần hơn với trạng thái mong muốn.

- **Controller** có thể tự thực hiện hành động; thường thấy hơn, trong Kubernetes, một **controller** sẽ gửi tin nhắn tới máy chủ API mang lại hiệu ứng hữu ích. Bạn sẽ thấy ví dụ về điều này bên dưới.

#### **Điều khiển qua Máy chủ API**
- **Controller Job** là một ví dụ về **controller** tích hợp sẵn của Kubernetes. Các **controller** tích hợp sẵn quản lý trạng thái bằng cách tương tác với máy chủ API của cluster.

- **Job** là một tài nguyên Kubernetes chạy một Pod, hoặc có thể là nhiều Pods, để thực hiện một nhiệm vụ và sau đó dừng lại.

(Một khi được lên lịch, các đối tượng Pod trở thành phần của trạng thái mong muốn cho một kubelet).

- Khi **controller Job** thấy một nhiệm vụ mới, nó đảm bảo rằng, ở đâu đó trong cluster của bạn, các kubelet trên một nhóm Nodes đang chạy số lượng Pods đúng để hoàn thành công việc. **Controller Job** không chạy bất kỳ Pod hoặc container nào. Thay vào đó, **controller Job** yêu cầu máy chủ API tạo hoặc xóa Pods. Các thành phần khác trong controller thực hiện theo thông tin mới (có Pods mới cần được lên lịch và chạy), và cuối cùng công việc được hoàn thành.

- Sau khi bạn tạo một **Job** mới, trạng thái mong muốn là cho **Job** đó được hoàn thành. **Controller Job** làm cho trạng thái hiện tại của **Job** đó gần hơn với trạng thái mong muốn: tạo Pods thực hiện công việc bạn muốn cho **Job** đó, để **Job** gần hơn với việc hoàn thành.

- Các **controller** cũng cập nhật các đối tượng cấu hình cho chúng. Ví dụ: một khi công việc được hoàn thành cho một **Job**, **controller Job** cập nhật đối tượng **Job** đó để đánh dấu nó đã hoàn thành.

(Điều này giống như cách một số bộ điều chỉnh nhiệt tắt đèn để chỉ ra rằng phòng của bạn hiện ở nhiệt độ bạn đã thiết lập).

#### **Điều khiển Trực tiếp**
- Trái ngược với **Job**, một số **controller** cần thực hiện thay đổi đối với những thứ bên ngoài cluster của bạn.

- Ví dụ, nếu bạn sử dụng một **vòng lặp điều khiển** để đảm bảo có đủ Nodes trong cluster của bạn, thì **controller** đó cần một thứ gì đó bên ngoài cluster hiện tại để thiết lập Nodes mới khi cần thiết.

- Các **controller** tương tác với trạng thái bên ngoài tìm trạng thái mong muốn từ máy chủ API, sau đó trực tiếp giao tiếp với một hệ thống bên ngoài để đưa trạng thái hiện tại gần hơn.

(Thực tế có một **controller** điều chỉnh quy mô các nodes trong cluster của bạn theo chiều ngang).

- Điểm quan trọng ở đây là **controller** thực hiện một số thay đổi để đạt được trạng thái mong muốn, sau đó báo cáo trạng thái hiện tại trở lại máy chủ API của cluster. Các **vòng lặp điều khiển** khác có thể quan sát dữ liệu được báo cáo và thực hiện các hành động của riêng mình.

- Trong ví dụ về bộ điều chỉnh nhiệt, nếu phòng rất lạnh thì một **controller** khác cũng có thể bật máy sưởi chống đóng băng. Đối với các cluster Kubernetes, controller gián tiếp làm việc với các công cụ quản lý địa chỉ IP, dịch vụ lưu trữ, API của nhà cung cấp dịch vụ đám mây và các dịch vụ khác bằng cách mở rộng Kubernetes để triển khai điều đó.

### **Trạng thái mong muốn và trạng thái hiện tại**
Kubernetes xem xét hệ thống theo góc nhìn điện toán đám mây, và do đó có thể xử lý các thay đổi.

Cụm của bạn có thể thay đổi bất kỳ lúc nào khi công việc diễn ra và các vòng lặp điều khiển tự động khắc phục lỗi. Điều này có nghĩa: cụm của bạn có thể không bao giờ đạt đến trạng thái ổn định.

Miễn là controller cụm đang chạy và có thể thực hiện các thay đổi có ích, tính ổn định của trạng thái tổng thể không quan trọng.

### **Thiết kế**
Theo nguyên lý thiết kế, Kubernetes sử dụng nhiều controller, mỗi controller quản lý một khía cạnh cụ thể của trạng thái cụm. Thông thường nhất, một vòng lặp điều khiển (hoặc bộ điềuk hiển) cụ thể sử dụng một loại tài nguyên làm trạng thái mong muốn của nó và có một loại tài nguyên khác mà nó quản lý để tạo ra trạng thái mong muốn đó.

_**Ví dụ:** Controller cho Jobs theo dõi các đối tượng Job (để phát hiện công việc mới) và các đối tượng Pod (để chạy Jobs, sau đó xem khi nào công việc hoàn thành). Trong trường hợp này, thứ gì đó khác tạo ra Jobs, trong khi Job controller tạo ra Pod._

{{% notice note %}}
Có thể có nhiều controller cùng tạo hoặc cập nhật chung một loại đối tượng. Thực tế, ở bên dưới, bộ điều khiển Kubernetes đảm bảo rằng chúng chỉ chú ý đến các tài nguyên được liên kết với tài nguyên điều khiển của chúng.\
![Kubernetes Controllers](../../../../images/part1/1/3/0003.png?featherlight=false&width=60pc)
Ví dụ, bạn có thể có Deployments và Jobs; cả hai đều tạo ra Pods. Bộ điều khiển Job không xóa các Pods mà Deployment của bạn đã tạo, vì có thông tin (nhãn) mà bộ điều khiển có thể sử dụng để phân biệt các Pods đó.
{{% /notice %}}

### **Những cách thức chạy bộ điều khiển**
Kubernetes đi kèm với một bộ điều khiển tích hợp chạy bên trong kube-controller-manager. Các bộ điều khiển tích hợp này cung cấp các hành vi cốt lõi quan trọng.

Bộ điều khiển Deployment và bộ điều khiển Job là các ví dụ về bộ điều khiển đi kèm như một phần của chính Kubernetes (bộ điều khiển "tích hợp"). Kubernetes cho phép bạn chạy một tầng điều khiển dự phòng, do đó nếu bất kỳ bộ điều khiển tích hợp nào bị lỗi, một phần khác của tầng điều khiển sẽ tiếp quản công việc.

Bạn cũng sẽ thấy các bộ điều khiển chạy bên ngoài tầng điều khiển để mở rộng Kubernetes. Hoặc, nếu muốn, bạn có thể tự viết một bộ điều khiển mới. Bạn có thể chạy bộ điều khiển của riêng mình dưới dạng một bộ Pod hoặc bên ngoài Kubernetes. Bộ điều khiển nào phù hợp nhất sẽ phụ thuộc vào chức năng của bộ điều khiển cụ thể đó.