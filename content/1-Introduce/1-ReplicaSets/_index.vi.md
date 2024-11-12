---
title: "ReplicaSets"
weight: 1
chapter: false
pre: "<b> 1.1 </b>"
---

Trong phần này, chúng ta sẽ tìm hiểu về các khái niệm sau:
- Replication Controller
- ReplicaSet

### Giới thiệu
#### Replica là gì ?
Trong Kubernetes, **Replica** là **_bản sao_** của một pod, cho phép các pod tự phục hồi. Giống như hầu hết các tiến trình (process) và dịch vụ (service), các pod cũng đứng trước nguy cơ bị lỗi, bị buộc dừng hoặc bị xóa. Ví dụ, các pod có thể bị lỗi và sau đó bị buộc dừng khi tài nguyên hệ thống giảm đột ngột và áp lực trên từng node tăng.

#### ReplicationController
`ReplicationController` đảm bảo số lượng bản sao pod luôn ở một giá trị xác định tại mọi thời điểm. Nếu số pod nhiều hơn giá trị xác định, ReplicationController sẽ hủy các pod dư. Nếu ít hơn, ReplicationController sẽ khởi tạo thêm pod. Không giống như các pod được tạo thủ công, các pod do ReplicationController duy trì sẽ tự động được thay thế nếu chúng bị lỗi, bị xóa hoặc bị buộc dừng. Ví dụ: (các) pod của bạn được tạo lại trên một node sau khi bảo trì đột xuất như nâng cấp hạt nhân. Vì lý do này, bạn nên sử dụng ReplicationController ngay cả khi ứng dụng chỉ yêu cầu một pod duy nhất.

Nói tóm lại, ReplicationController đảm bảo rằng một pod hoặc một tập hợp pod đồng nhất luôn hoạt động và khả dụng. ReplicationController tương tự như một trình giám sát tiến trình, nhưng thay vì giám sát các tiến trình riêng lẻ trên một máy/node nhất, ReplicationController sẽ giám sát nhiều pod trên nhiều node.

ReplicationController thường được viết tắt là "rc" và là một lệnh tắt trong các lệnh `kubectl`.

#### ReplicaSet
Tương tự như ReplicationController, mục đích của `ReplicaSet` là duy trì một tập hợp các bản sao Pod đang chạy tại bất kỳ thời điểm nào. Do đó, nó thường được sử dụng để đảm bảo một số lượng xác định các Pod giống nhau luôn khả dụng.

Một ReplicaSet được định nghĩa bằng các trường, bao gồm một `Selector` (hàm chọn) chỉ định cách nhận diện các Pod nó đuọc phép kiểm soát, số lượng bản sao pod cần duy trì và một mẫu (template) pod chỉ định dữ liệu của các Pod mới cần được tạo để đáp ứng tiêu chí về số lượng bản sao. Sau đó, ReplicaSet thực hiện mục đích của mình bằng cách tạo và xóa các Pod khi cần để đạt được số lượng mong muốn. Khi một ReplicaSet cần tạo các Pod mới, nó sẽ sử dụng mẫu Pod đã xác định.

Một ReplicaSet được liên kết với các Pod của nó thông qua trường metadata.ownerReferences của Pod, trường này chỉ định tài nguyên nào sở hữu đối tượng hiện tại. Tất cả các Pod trong ReplicaSet đều sở hữu thông tin nhận dạng ReplicaSet trong trường ownerReferences của chúng. Thông qua liên kết này, ReplicaSet biết được trạng thái của các Pod nó đang duy trì và lập kế hoạch phù hợp.

Một ReplicaSet xác định các Pod mới để thu thập bằng cách sử dụng Selector của nó. Nếu có một Pod không có OwnerReference hoặc OwnerReference không phải là Controller nhưng có thuộc tính khớp với Selector của ReplicaSet, thì nó sẽ được ReplicaSet nói trên thu thập ngay lập tức.

#### So sánh ReplicaSet ReplicationController
ReplicaSets là khái niệm kế thừa và thay thế ReplicationControllers. Cả hai đều có cùng mục đích và cách hoạt động tương tự nhau, ngoại trừ việc ReplicationController không hỗ trợ các yêu cầu Selector dựa trên tập hợp như mô tả trong hướng dẫn sử dụng `label` (nhãn). Do đó, ReplicaSets được ưa chuộng hơn ReplicationControllers.

### Tạo ReplicationController
  - Tệp định nghĩa ReplicationController

```yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: myapp-rc
  labels:
    app: myapp
    type: front-end
spec:
 template:
    metadata:
      name: myapp-pod
      labels:
        app: myapp
        type: front-end
    spec:
     containers:
     - name: nginx-container
       image: nginx
 replicas: 3
```

Để tạo **replication controller**

```
$ kubectl create -f rc-definition.yaml

```

Để liệt kê tất cả các **replication controllers**

```bash
$ kubectl get replicationcontroller
```

Để liệt kê các pod được khởi chạy bởi **replication controller**

```bash
$ kubectl get pods
```

### Tạo một **ReplicaSet**
  - Tệp Định nghĩa **ReplicaSet**

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: myapp-replicaset
  labels:
    app: myapp
    type: front-end
spec:
 template:
    metadata:
      name: myapp-pod
      labels:
        app: myapp
        type: front-end
    spec:
     containers:
     - name: nginx-container
       image: nginx
 replicas: 3
 selector:
   matchLabels:
    type: front-end
```

**ReplicaSet** yêu cầu thêm định nghĩa **selector** so với **Replication Controller**.
Để tạo **ReplicaSet**

```bash
$ kubectl create -f replicaset-definition.yaml
```

Để liệt kê tất cả các **ReplicaSet**

```bash
$ kubectl get replicaset
```

Để liệt kê các pod được khởi chạy bởi **ReplicaSet**

```bash
$ kubectl get pods
```

### Mở rộng ReplicaSet
- Có nhiều cách để mở rộng **ReplicaSet**

  - **Cách 1:** Cập nhật số lượng bản sao trong file định nghĩa **replicaset-definition.yaml**. Ví dụ: **replicas: 6** và sau đó chạy

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: myapp-replicaset
  labels:
    app: myapp
    type: front-end
spec:
 template:
    metadata:
      name: myapp-pod
      labels:
        app: myapp
        type: front-end
    spec:
     containers:
     - name: nginx-container
       image: nginx
 replicas: 6
 selector:
   matchLabels:
    type: front-end

```

```bash
$ kubectl apply -f replicaset-definition.yaml
```

  - **Cách 2:** sử dụng lệnh **kubectl scale**.

```bash
$ kubectl scale --replicas=6 -f replicaset-definition.yaml
```

  Bạn cũng có thể sử dụng lệnh **kubectl scale** với loại tài nguyên và tên đối tượng tài nguyên thay vì với file định nghĩa:

```bash
$ kubectl scale --replicas=6 replicaset myapp-replicaset
```

### Tài liệu Tham khảo:
- [Kubernetes: Replica](https://sematext.com/glossary/kubernetes-replica/)
- [Kubernetes: Replicaset](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/)
- [Kubernetes: Replication Controller](https://kubernetes.io/docs/concepts/workloads/controllers/replicationcontroller/)
