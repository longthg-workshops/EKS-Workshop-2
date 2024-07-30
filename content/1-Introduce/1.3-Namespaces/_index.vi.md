---
title: "Namespaces"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: "<b> 1.3 </b>"
---

#### Giới thiệu về Namespaces trong Kubernetes
Trong phần này, chúng ta sẽ tìm hiểu về **Namespaces**.

Kể từ khi bắt đầu workshop, đến thời điểm này, chúng ta đã tạo ra các đối tượng như **PODs**, **Deployments** và **Services** trong cluster của chúng ta. Mọi thứ chúng ta đã làm, đều nằm trong cùng một **NAMESPACE**.

**Namespace** mặc định trong Kubernetes là không gian mặc định được tạo tự động khi Kubernetes được thiết lập ban đầu.

```
kubectl get namespaces
```

Bạn cũng có thể tạo các **Namespaces** của riêng mình.

```
kubectl create namespace dev
```

Để liệt kê các **POD** trong **namespace** mặc định:

```
kubectl get pods
```

Để liệt kê các **POD** trong một **namespace** khác, sử dụng lệnh `kubectl get pods` cùng với cờ hoặc đối số `--namespace`.

```
kubectl get pods --namespace=kube-system
```

Khi tạo một **POD** từ một tệp định nghĩa **POD**, **POD** sẽ được tạo trong **namespace** mặc định.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
     app: myapp
     type: front-end
spec:
  containers:
  - name: nginx-container
    image: nginx
```

```
kubectl create -f pod-definition.yaml
```

Để tạo **POD** với tệp định nghĩa **POD** trong một **namespace** khác, sử dụng tùy chọn `--namespace`.

```
kubectl create -f pod-definition.yaml --namespace=dev
```

Nếu bạn muốn đảm bảo rằng **POD** này luôn được tạo trong môi trường dev, thậm chí khi không chỉ định trong dòng lệnh, bạn có thể di chuyển định nghĩa `--namespace` vào tệp định nghĩa **POD**.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  namespace: dev
  labels:
     app: myapp
     type: front-end
spec:
  containers:
  - name: nginx-container
    image: nginx
```

Để tạo một **namespace** mới, tạo một định nghĩa **namespace** như dưới đây và sau đó chạy lệnh `kubectl create`.

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: dev
```

```
kubectl create -f namespace-dev.yaml
```

Hoặc có thể sử dụng lệnh ngắn gọn:

```
kubectl create namespace dev
```

Mặc định, chúng ta sẽ ở trong một **namespace** mặc định. Để chuyển đổi sang một **namespace** cụ thể một cách vĩnh viễn, chạy lệnh sau:

```
kubectl config set-context $(kubectl config current-context) --namespace=dev
```

Để xem các **POD** trong tất cả các **namespaces**:

```
kubectl get pods --all-namespaces
```

Để giới hạn tài nguyên trong một **namespace**, tạo một **resource quota**. Để tạo một, bắt đầu với tệp định nghĩa **ResourceQuota**.

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-quota
  namespace: dev
spec:
  hard:
    pods: "10"
    requests.cpu: "4"
    requests.memory: 5Gi
    limits.cpu: "10"
    limits.memory: 10Gi
```

```
kubectl create -f compute-quota.yaml
```

#### Tài liệu tham khảo Kubernetes:

- [Kubernetes - Overview - Working with Objects - Namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)
- [Kubernetes - Tasks - Administer Cluster - Namespaces Walkthrough](https://kubernetes.io/docs/tasks/administer-cluster/namespaces-walkthrough/)
- [Kubernetes - Tasks - Administer Cluster - Namespaces](https://kubernetes.io/docs/tasks/administer-cluster/namespaces/)
- [Kubernetes - Tasks - Administer Cluster - Manage Resources - Quota Memory CPU - Namespace](https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/quota-memory-cpu-namespace/)
- [Kubernetes - Tasks - Access Application Cluster - List All Running Container Images](https://kubernetes.io/docs/tasks/access-application-cluster/list-all-running-container-images/)
