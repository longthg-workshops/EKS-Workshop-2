---
title: "ReplicaSets"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: "<b> 1.1 </b>"
---

#### ReplicaSets

Trong phần này, chúng ta sẽ tìm hiểu về các khái niệm sau:

#### **ReplicaSet**
**Controllers** là bộ não đằng sau **Kubernetes**.
**Replica** là gì và tại sao chúng ta cần một **replication controller**?

#### **Sự khác biệt giữa ReplicaSet và Replication Controller**
**Replication Controller** là công nghệ cũ được thay thế bằng **ReplicaSet**.
**ReplicaSet** là cách mới để thiết lập replication.
File Định nghĩa **Replication Controller** có dạng:

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

```

$ kubectl get replicationcontroller

```

Để liệt kê các pod được khởi chạy bởi **replication controller**

```
$ kubectl get pods

```

#### Tạo một **ReplicaSet**
File Định nghĩa **ReplicaSet**

```
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

```
$ kubectl create -f replicaset-definition.yaml

```

Để liệt kê tất cả các **ReplicaSet**

```
$ kubectl get replicaset

```

Để liệt kê các pod được khởi chạy bởi **ReplicaSet**

```
$ kubectl get pods

```

#### Nhãn và Selector
Vấn đề với **Nhãn và Selector** là gì? Tại sao chúng ta đánh dấu các pod và đối tượng trong **Kubernetes**?

- Làm thế nào để mở rộng **ReplicaSet**
- Có nhiều cách để mở rộng **ReplicaSet**
- Cách đầu tiên là cập nhật số lượng bản sao trong file định nghĩa **replicaset-definition.yaml**. Ví dụ: **replicas: 6** và sau đó chạy

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

```
$ kubectl apply -f replicaset-definition.yaml
```

Cách thứ hai là sử dụng lệnh **kubectl scale**.

```
$ kubectl scale --replicas=6 -f replicaset-definition.yaml

```

Cách thứ ba là sử dụng lệnh **kubectl scale** với loại và tên

```
$ kubectl scale --replicas=6 replicaset myapp-replicaset

```

#### Tài liệu Tham khảo K8s:

- [Kubernetes: Replicaset](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/)
- [Kubernetes: Replication Controller](https://kubernetes.io/docs/concepts/workloads/controllers/replicationcontroller/)
