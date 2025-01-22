---
title: "Namespaces"
weight: 1
chapter: false
pre: "<b> 1.1 </b>"
---

### Giới thiệu về Namespaces

Trong phần này, chúng ta sẽ tìm hiểu về **Namespace**.

Trong các workshop về EKS, chúng ta sẽ phải tạo các đối tượng như **Pod**, **Deployment** và **Service** trong cluster của chúng ta. Tất cả các đối tượng trên, đều nằm trong cùng một **namespace**.

**Namespace** mặc định trong Kubernetes là không gian mặc định được tạo tự động khi Kubernetes mới được cài đặt và thiết lập.

### Các lệnh

Để xem toàn bộ các namespace, hãy sử dụng lệnh sau:

```bash
kubectl get namespaces
```

Bạn cũng có thể tạo các **Namespaces** của riêng mình.

```bash
kubectl create namespace dev
```

Để liệt kê các **Pod** trong **namespace** mặc định:

```bash
kubectl get pods
```

Để liệt kê các **Pod** trong một **namespace** khác, sử dụng lệnh `kubectl get pods` cùng với cờ hoặc đối số `--namespace`.

```bash
kubectl get pods --namespace=kube-system
```
_* Thay thế kube-system bằng bất kỳ namespace nào bạn muốn xem_

Khi tạo một **Pod** từ một tệp định nghĩa **Pod**, **Pod** sẽ được tạo trong **namespace** mặc định nếu không có trường **namespace**.

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

```bash
kubectl create -f pod-definition.yaml
```

Để tạo **Pod** với cùng tệp định nghĩa **Pod** trong một **namespace** khác, sử dụng tùy chọn `--namespace`.

```bash
kubectl create -f pod-definition.yaml --namespace=dev
```

Nếu bạn muốn đảm bảo rằng **Pod** này luôn được tạo trong môi trường dev, thậm chí khi không chỉ định trong dòng lệnh, bạn có thể di chuyển định nghĩa `--namespace` vào tệp định nghĩa **Pod**.

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

```bash
kubectl create -f namespace-dev.yaml
```

Hoặc có thể sử dụng lệnh ngắn gọn:

```bash
kubectl create namespace dev
```

Mặc định, chúng ta sẽ ở trong một **namespace** mặc định. Để chuyển đổi sang một **namespace** cụ thể một cách vĩnh viễn, chạy lệnh sau:

```bash
kubectl config set-context $(kubectl config current-context) --namespace=dev
```

Để xem các **Pod** trong tất cả các **namespaces**:

```bash
kubectl get pods --all-namespaces
```

Để giới hạn tài nguyên trong một **namespace**, tạo một **resource quota**. Để tạo một quota, bắt đầu với tệp định nghĩa **ResourceQuota**.

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

```bash
kubectl create -f compute-quota.yaml
```

#### Tài liệu tham khảo:

- [Kubernetes - Overview - Working with Objects - Namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)
- [Kubernetes - Tasks - Administer Cluster - Namespaces Walkthrough](https://kubernetes.io/docs/tasks/administer-cluster/namespaces-walkthrough/)
- [Kubernetes - Tasks - Administer Cluster - Namespaces](https://kubernetes.io/docs/tasks/administer-cluster/namespaces/)
- [Kubernetes - Tasks - Administer Cluster - Manage Resources - Quota Memory CPU - Namespace](https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/quota-memory-cpu-namespace/)
- [Kubernetes - Tasks - Access Application Cluster - List All Running Container Images](https://kubernetes.io/docs/tasks/access-application-cluster/list-all-running-container-images/)
