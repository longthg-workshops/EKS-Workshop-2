---
title: "Deployment trong Kubernetes"
weight: 2
chapter: false
pre: "<b> 1.2 </b>"
---

Trong phần này, chúng ta sẽ tìm hiểu về khái niệm **deployment** trong Kubernetes.

### Giới thiệu về Deployment
#### Deployment là gì ?

Deployment (Triển khai, Bản triển khai) cung cấp các cập nhật khai báo cho Pod và ReplicaSet.

Khi mô tả trạng thái mong muốn trong Deployment, Deployment Controller sẽ thay đổi trạng thái thực tế thành trạng thái mong muốn với sự kiểm soát về tốc độ. Bạn có thể định nghĩa Deployment để tạo ReplicaSet mới, hoặc xóa Deployment hiện có và áp dụng tất cả tài nguyên của chúng bằng Deployment mới.

{{% notice note %}}
Không được quản lý ReplicaSets do Deployment sở hữu. Hãy cân nhắc khởi tạo Issue trong [kho GitHub chính của Kubernetes](https://github.com/kubernetes/kubernetes/issues) nếu trường hợp sử dụng của bạn không được đề cập ở phần tiếp theo.
{{% /notice %}}

#### Các trường hợp sử dụng
Sau đây là một số trường hợp sử dụng điển hình cho Triển khai:

- Tạo Triển khai để triển khai ReplicaSet. ReplicaSet tạo Pod ở chế độ nền. Kiểm tra trạng thái của triển khai để xem có thành công hay không.

- Khai báo trạng thái mới của Pod bằng cách cập nhật PodTemplateSpec của Triển khai. Một ReplicaSet mới được tạo và Triển khai quản lý việc di chuyển Pod từ ReplicaSet cũ sang ReplicaSet mới với tốc độ được kiểm soát. Mỗi ReplicaSet mới sẽ cập nhật bản sửa đổi của Triển khai.

- Quay lại bản sửa đổi Triển khai trước đó nếu trạng thái hiện tại của Triển khai không ổn định. Mỗi lần quay lại sẽ cập nhật bản sửa đổi của Triển khai.

- Mở rộng Triển khai để tạo điều kiện tải nhiều hơn.

- Tạm dừng triển khai Triển khai để áp dụng nhiều bản sửa lỗi cho PodTemplateSpec của nó, sau đó tiếp tục để bắt đầu triển khai mới.

- Sử dụng trạng thái của Triển khai như một chỉ báo cho biết quá trình triển khai đã bị kẹt.

- Dọn dẹp các ReplicaSet cũ mà bạn không cần nữa.


### Tạo một Deployment
#### Tệp định nghĩa Deployment

**`deployment-definition.yaml`**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

Trong ví dụ này:

- Một Deployment có tên `nginx-deployment` được tạo, được chỉ định bởi trường `.metadata.name`. Tên này sẽ trở thành cơ sở cho ReplicaSet và Pod được tạo sau đó. Xem Viết Deployment Spec để biết thêm chi tiết.

- Deployment tạo ra một ReplicaSet tạo ra ba Pod được sao chép, được chỉ định bởi trường `.spec.replicas`.

- Trường `.spec.selector` xác định cách ReplicaSet được tạo tìm Pod nào để quản lý. Trong trường hợp này, bạn chọn một nhãn được xác định trong mẫu Pod (ứng dụng: nginx). Tuy nhiên, có thể áp dụng các quy tắc lựa chọn phức tạp hơn, miễn là bản thân mẫu Pod đáp ứng quy tắc.

{{% notice note %}}
Trường `.spec.selector.matchLabels` là hệ ánh xạ các cặp {key,value}. Một {key,value} duy nhất trong hệ ánh xạ matchLabels tương đương với một phần tử của matchExpressions, có trường khóa là "key", toán tử là "In" và mảng các giá trị ​​chỉ chứa "value". Tất cả các yêu cầu, từ cả matchLabels và matchExpressions, phải được đáp ứng để khớp.
{{% /notice %}}

- Một trường mẫu chứa các trường con sau:

  - Các Pod được gắn nhãn `app: nginx` nhờ trường `.metadata.labels`.

  - Đặc tả của mẫu Pod, hoặc trường `.template.spec`, cho biết rằng các Pod chỉ chạy một container, `nginx`, được khởi tạo từ ảnh **_nginx_** từ Docker Hub, phiên bản 1.14.2.

  - Tạo một container và đặt tên là nginx bằng cách sử dụng trường `.spec.template.spec.containers[0].name`.

#### Các lệnh

Sau khi tệp này đã sẵn sàng, tạo **deployment** bằng cách sử dụng tệp định nghĩa **deployment**:

```bash
$ kubectl create -f deployment-definition.yaml
```

Để xem **deployment** đã được tạo:

```bash
$ kubectl get deployment
```

**Deployment** tự động tạo một **ReplicaSet**. Để xem các **ReplicaSet**:

```bash
$ kubectl get replicaset
```

Các **ReplicaSet** cuối cùng sẽ tạo ra các **POD**. Để xem các **POD**:

```bash
$ kubectl get pods
```

Để xem tất cả các đối tượng cùng một lúc:

```bash
$ kubectl get all
```

### Tài liệu tham khảo

- [Tìm hiểu về Deployment (bản triển khai)](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)
- [Triển khai một ứng dụng](https://kubernetes.io/docs/tutorials/kubernetes-basics/deploy-app/deploy-intro/)
- [Quản lý các Deployment](https://kubernetes.io/docs/concepts/cluster-administration/manage-deployment/)
- [Làm việc với các đối tượng Kubernetes](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/)