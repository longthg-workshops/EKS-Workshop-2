---
title: "Dịch vụ Kubernetes"
date: "2024-04-03"
weight: 4
chapter: false
pre: "<b> 1.4 </b>"
---

#### Dịch vụ (Services)

Dịch vụ **Kubernetes** cho phép giao tiếp giữa các thành phần khác nhau trong và ngoài ứng dụng.

Hãy xem qua một số khía cạnh khác về mạng lưới.

#### Giao Tiếp Bên Ngoài (External Communication)

Làm thế nào chúng ta, như một người dùng bên ngoài, có thể truy cập trang web?

- Từ node (Có thể tiếp cận ứng dụng như mong đợi)
- Từ thế giới bên ngoài (Điều này là mong đợi của chúng ta, mà không có gì ở giữa thì sẽ không thể tiếp cận được ứng dụng)

#### Các Loại Dịch Vụ (Service Types)
Có 3 loại dịch vụ trong **Kubernetes**:

#### 1. NodePort

Nơi mà dịch vụ làm cho một cổng nội bộ có thể tiếp cận trên một cổng trên **node**.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  type: NodePort
  ports:
  - targetPort: 80
    port: 80
    nodePort: 30008
```

Để kết nối dịch vụ với pod:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  type: NodePort
  ports:
  - targetPort: 80
    port: 80
    nodePort: 30008
  selector:
    app: myapp
    type: front-end
```

Để tạo dịch vụ:

```sh
$ kubectl create -f service-definition.yaml
```

Để liệt kê các dịch vụ:

```sh
$ kubectl get services
```

Để truy cập ứng dụng từ dòng lệnh thay vì trình duyệt web:

```sh
$ curl http://192.168.1.2:30008
```

#### 2. ClusterIP

Trong trường hợp này, dịch vụ tạo một **Địa chỉ IP** ảo trong cụm để cho phép giao tiếp giữa các dịch vụ khác nhau như một bộ máy chủ frontend và một bộ máy chủ backend.

#### 3. LoadBalancer

Nơi mà dịch vụ cung cấp một trình cân bằng tải cho ứng dụng của chúng ta trong các nhà cung cấp đám mây được hỗ trợ.
