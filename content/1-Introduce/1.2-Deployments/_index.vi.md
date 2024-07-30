---
title: "Triển khai (Deployments) trong Kubernetes"
date: "2024-04-03"
weight: 2
chapter: false
pre: "<b> 1.2 </b>"
---

#### Triển khai (Deployments) trong Kubernetes

Trong phần này, chúng ta sẽ tìm hiểu về các **triển khai (deployments)** trong Kubernetes.

**Triển khai (Deployment)** là một đối tượng trong Kubernetes.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-deployment
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

Sau khi tệp này đã sẵn sàng, tạo **triển khai** bằng cách sử dụng tệp định nghĩa **triển khai**:

```bash
$ kubectl create -f deployment-definition.yaml
```

Để xem **triển khai** đã được tạo:

```bash
$ kubectl get deployment
```

**Triển khai** tự động tạo một **ReplicaSet**. Để xem các **ReplicaSet**:

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

#### Tài liệu tham khảo về Kubernetes:

- [Triển khai (Deployments)](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)
- [Giới thiệu về triển khai](https://kubernetes.io/docs/tutorials/kubernetes-basics/deploy-app/deploy-intro/)
- [Quản lý triển khai](https://kubernetes.io/docs/concepts/cluster-administration/manage-deployment/)
- [Làm việc với các đối tượng Kubernetes](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/)