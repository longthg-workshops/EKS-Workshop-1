---
title: "Container Runtime"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: "<b> 1.2 </b>"
---

Phần này sẽ cung cấp một cái nhìn tổng quan về **_container runtime_**.

### Container runtime là gì ?
Container runtime là phần mềm nền tảng để container hoạt động trong hệ thống máy chủ. Container runtime chịu trách nhiệm cho mọi thứ, từ việc kéo các container image từ registry và quản lý vòng đời của chúng, đến chạy container trên hệ thống của bạn.

### Chức năng và vai trò
Việc hiểu các chức năng và vai trò cốt lõi rất quan trọng đối với việc đánh giá cách Container Runtime hỗ trợ việc thực hiện và quản lý container một cách liền mạch. Nhìn chung, một container runtime thực hiện những việc sau:

#### Thực thi container
Container runtime chủ yếu thực thi container thông qua một quy trình nhiều bước. Bước đầu tiên, quy trình này bắt đầu bằng cách tạo container và môi trường của nó dựa trên một container image chứa ứng dụng cùng các phần phụ thuộc. Sau khi tạo, runtime chạy các container, khởi động ứng dụng và đảm bảo chức năng phù hợp. Ngoài ra, runtime quản lý vòng đời của container, bao gồm theo dõi tình trạng, khởi động lại nếu chúng bị lỗi và dọn dẹp tài nguyên sau khi các container không còn được sử dụng nữa.

#### Tương tác với hệ điều hành chủ
Các container runtime tương tác chặt chẽ với hệ điều hành chủ. Chúng tận dụng nhiều tính năng khác nhau của hệ điều hành, như **namespace** và **cgroup**, để cô lập và quản lý tài nguyên cho từng container. Sự cô lập này đảm bảo rằng các quy trình bên trong container không thể làm gián đoạn máy chủ hoặc các container khác, duy trì môi trường an toàn và ổn định.

#### Cấp phát và quản lý tài nguyên
Các container runtime là một phần thiết yếu trong quản lý tài nguyên vì chúng phân bổ và điều chỉnh CPU, bộ nhớ và quá trình I/O cho từng container để ngăn chặn tình trạng độc chiếm tài nguyên, đặc biệt là trong môi trường multi-tenant (nhiều bên sử dụng). Cách container runtime xử lý trơn tru quá trình chạy, vòng đời và tương tác giữa container và hệ điều hành máy chủ là chìa khóa để container hóa trở thành một phần quan trọng trong bối cảnh phát triển phần mềm ngày nay.

### Container Runtime - Bậc thấp và Bậc cao
Khi nhắc đến container runtime, người ta có thể nghĩ đến một danh sách các ví dụ: runc, lxc, lmctfy, Docker (**`containerd`**), rkt, **cri-o**. Mỗi loại này được xây dựng cho các tình huống khác nhau và triển khai các tính năng khác nhau. Một số loại, như **`containerd`** và **cri-o**, thực sự sử dụng runc để chạy container nhưng triển khai quản lý image và API ở trên. Bạn có thể coi các tính năng này – bao gồm vận chuyển image, quản lý image, giải nén image và API – là các tính năng bậc cao so với triển khai bậc thấp của runc.

Với điều đó trong đầu, bạn có thể thấy rằng không gian container runtime khá phức tạp. Mỗi runtime bao gồm các phần khác nhau trên phổ bậc thấp-cao. Sau đây là một sơ đồ rất chủ quan:

![CR level spectrum](../../../images/part1/2/runtimes.png?width=50pc)

Do đó, để phù hợp thực tế, các container runtime thật sự chỉ tập trung vào việc chạy container thường được gọi là "container runtime bậc thấp". Runtime hỗ trợ nhiều tính năng bậc cao hơn, như quản lý image và API gRPC/Web, thường được gọi là "công cụ container bậc cao", "container runtime bậc cao" hoặc thường chỉ là "container runtime". Phần này sẽ gọi chúng là "container runtime bậc cao". Điều quan trọng cần lưu ý là runtime bậc thấp và runtime bậc cao về cơ bản là những thứ khác nhau giải quyết các vấn đề khác nhau.

Thông thường, các nhà phát triển muốn chạy ứng dụng trong container sẽ cần nhiều hơn các tính năng runtime bậc thấp cung cấp. Họ cần API và các tính năng xoay quanh việc định dạng, quản lý và chia sẻ các image. Các tính năng này được cung cấp bởi runtime bậc cao. Runtime bậc thấp không cung cấp đủ tính năng cho mục đích sử dụng hàng ngày này. Do đó, những người duy nhất thực sự sử dụng runtime bậc thấp sẽ chỉ có các nhà phát triển triển khai runtime bậc cao hơn và các công cụ cho container.

Các nhà phát triển triển khai runtime bậc thấp sẽ nói rằng các runtime bậc cao hơn như **`containerd`** và **cri-o** không thực sự là container runtime, vì theo họ, chúng "ủy thác" việc triển khai chạy container cho runc. Tuy nhiên, từ góc độ người dùng, chúng là như một thành phần duy nhất cung cấp khả năng chạy container.

![CR levels relation](../../../images/part1/2/runtime-architecture.png?width=50pc)

### Container runtimes trên Kubernetes
Kubernetes runtime là container runtime bậc cao hỗ trợ Giao diện container runtime (CRI). CRI được giới thiệu trong Kubernetes 1.5 và hoạt động như một cầu nối giữa kubelet và container runtime. Container runtime bậc cao muốn tích hợp với Kubernetes được kỳ vọng sẽ triển khai CRI. Runtime được kỳ vọng sẽ xử lý việc quản lý image và hỗ trợ các pod Kubernetes, cũng như quản lý các container riêng lẻ và do đó, được coi là runtime bậc cao theo phân loại ở trên.

Để hiểu thêm về CRI, chúng ta nên xem lại kiến ​​trúc Kubernetes tổng thể một lần nữa. Kubelet là một tác nhân nằm trên mỗi nút worker trong cụm Kubernetes. Kubelet chịu trách nhiệm quản lý khối lượng công việc container cho nút của nó. Khi thực sự chạy khối lượng công việc, kubelet sử dụng CRI để giao tiếp với container runtime đang chạy trên cùng một nút đó. Theo cách này, CRI chỉ đơn giản là một lớp trừu tượng hoặc API cho phép bạn chuyển đổi các triển khai container runtime thay vì tích hợp chúng vào kubelet.

![KubeCR](../../../images/part1/2/CRI.png?width=50pc)

Dưới đây là một số runtime dùng được trên Kubernetes:

#### Docker
Docker là một trong những container runtime nguồn mở đầu tiên. Nó được phát triển bởi công ty nền tảng dịch vụ dotCloud và được sử dụng để chạy các ứng dụng web của người dùng trong container.

Docker là container runtime kết hợp xây dựng, đóng gói, chia sẻ và chạy container. Docker có kiến ​​trúc máy khách/máy chủ và ban đầu được xây dựng dưới dạng daemon nguyên khối, `dockerd` và ứng dụng máy khách docker. Daemon cung cấp hầu hết logic để xây dựng container, quản lý image và chạy container, cùng với API. Máy khách dòng lệnh có thể được chạy để gửi lệnh và lấy thông tin từ daemon.

Docker ban đầu triển khai cả tính năng runtime bậc cao và bậc thấp, song các phần đó đã được chia thành các dự án riêng biệt là `runc` và ``containerd``. Docker hiện bao gồm daemon `dockerd` và daemon `docker-`containerd`` cùng với `docker-runc`. `docker-`containerd`` và `docker-runc` chỉ là các phiên bản được Docker đóng gói của `containerd` và runc nguyên bản.

`dockerd` cung cấp các tính năng như xây dựng image và `dockerd` sử dụng docker-`containerd` để cung cấp các tính năng như quản lý image và chạy container. Ví dụ, bước xây dựng của Docker thực chất chỉ là một số logic diễn giải Dockerfile, chạy các lệnh cần thiết trong container bằng `containerd` và lưu hệ thống tệp container kết quả dưới dạng image.

#### containerd
`containerd` là một runtime cấp cao được tách ra từ Docker. Giống như runc, được tách ra thành phần runtime cấp thấp, `containerd` được tách ra thành phần runtime cấp cao của Docker. `containerd` triển khai việc tải xuống image, quản lý chúng và chạy các container từ image. Khi cần chạy một container, nó sẽ giải nén image thành một gói OCI runtime và shell ra runc để chạy nó.

`containerd` cũng cung cấp một API và ứng dụng client dùng để tương tác với nó. CLI của `containerd` là `ctr`.

`ctr` có thể được sử dụng để yêu cầu `containerd` kéo một container image:

```bash
sudo ctr images pull docker.io/library/redis:latest
```

Liệt kê các image của bạn:

```bash
sudo ctr images list
```

Khởi tạo container từ image:

```bash
sudo ctr container create docker.io/library/redis:latest redis
```

Liệt kê các container đang chạy

```bash
sudo ctr container list
```

Dừng container:

```bash
sudo ctr container delete redis
```

Các lệnh này tương tự như cách người dùng tương tác với Docker. Tuy nhiên, trái ngược với Docker, `containerd` chỉ tập trung vào việc chạy container, do đó nó không cung cấp cơ chế để xây dựng container. Docker tập trung vào các trường hợp sử dụng của người dùng cuối và nhà phát triển, trong khi `containerd` tập trung vào các trường hợp sử dụng cho vận hành, chẳng hạn như chạy container trên máy chủ. Các tác vụ như xây dựng container image được giao cho các công cụ khác.

#### cri-o
cri-o là một CRI runtime nhẹ được tạo ra như một runtime bậc cao dành riêng cho Kubernetes. Nó hỗ trợ quản lý các image tương thích với OCI và kéo từ bất kỳ OCI image registry nào tương thích. Nó hỗ trợ runc và Clear Containers như các runtime bậc thấp. Về mặt lý thuyết, nó hỗ trợ các runtime bậc thấp tương thích với OCI khác, nhưng dựa vào khả năng tương thích với giao diện dòng lệnh runc OCI, vì vậy trên thực tế nó không linh hoạt bằng API `shim` của `containerd`.

```bash
cri-o’s endpoint is at /var/run/crio/crio.sock by default so you can configure crictl like so.

cat <<EOF | sudo tee /etc/crictl.yaml
runtime-endpoint: unix:///var/run/crio/crio.sock
EOF
```

### Tương tác với CRI
Ta có thể tương tác trực tiếp với CRI runtime bằng công cụ `crictl`. `crictl` cho phép gửi tin nhắn gRPC đến CRI runtime trực tiếp từ dòng lệnh. Chúng ta có thể sử dụng công cụ này để gỡ lỗi và kiểm tra các bản triển khai CRI mà không cần khởi động cụm Kubernetes hoàn chỉnh. Bạn có thể tải xuống tệp nhị phân `crictl` từ trang phát hành cri-tools trên GitHub.

Bạn có thể định cấu hình `crictl` bằng cách tạo tệp cấu hình trong /etc/crictl.yaml. Tại đây, bạn nên chỉ định gRPC endpoint của runtime là tệp socket Unix (unix:///path/to/file) hoặc TCP endpoint (tcp://\<host\>:\<port\>). Chúng ta sẽ sử dụng `containerd` cho ví dụ này:

```bash
cat <<EOF | sudo tee /etc/crictl.yaml
runtime-endpoint: unix:///run/`containerd`/`containerd`.sock
EOF
```

Hoặc bạn có thể xác định cụ thể runtime endpoint cho mỗi lần chạy lệnh:

```bash
crictl --runtime-endpoint unix:///run/`containerd`/`containerd`.sock …
```

Let’s run a pod with a single container with crictl. First you would tell the runtime to pull the nginx image you need since you can’t start a container without the image stored locally.

```bash
sudo crictl pull nginx
```

Hãy chạy một pod với một container duy nhất bằng crictl. Đầu tiên, bạn sẽ yêu cầu runtime kéo image nginx mà bạn cần vì bạn không thể khởi động một container mà không có image được lưu trữ cục bộ.

```bash
cat <<EOF | tee sandbox.json
{
    "metadata": {
        "name": "nginx-sandbox",
        "namespace": "default",
        "attempt": 1,
        "uid": "hdishd83djaidwnduwk28bcsb"
    },
    "linux": {
    },
    "log_directory": "/tmp"
}
EOF
```
Sau đó tạo sandbox cho pod. Chúng ta sẽ lưu ID của sandbox vào biến SANDBOX_ID.

```bash
SANDBOX_ID=$(sudo crictl runp --runtime runsc sandbox.json)
Next we will create a container creation request in a JSON file.
```

```bash
cat <<EOF | tee container.json
{
  "metadata": {
      "name": "nginx"
    },
  "image":{
      "image": "nginx"
    },
  "log_path":"nginx.0.log",
  "linux": {
  }
}
EOF
```
Tiếp đến chúng ta có thể tạo và khởi động container bên trong Pod mà chúng ta đã tạo trước đó.

```bash
CONTAINER_ID=$(sudo crictl create ${SANDBOX_ID} container.json sandbox.json)
sudo crictl start ${CONTAINER_ID}
```

Dùng lệnh sau để xem thông tin pod đang chạy:
```bash
sudo crictl inspectp ${SANDBOX_ID}
```

… và container đang chạy:

```bash
sudo crictl inspect ${CONTAINER_ID}
```
Dọn dẹp bằng cách dừng và xóa container:

```bash
sudo crictl stop ${CONTAINER_ID}
sudo crictl rm ${CONTAINER_ID}
```

Và cuối cùng là dừng và xóa pod:

```bash
sudo crictl stopp ${SANDBOX_ID}
sudo crictl rmp ${SANDBOX_ID}
```

### Tham khảo

1. https://www.wiz.io/academy/container-runtimes
2. https://www.ianlewis.org/en/container-runtimes-part-1-introduction-container-r
3. https://www.ianlewis.org/en/container-runtimes-part-4-kubernetes-container-run