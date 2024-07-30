---
title: "ClusterIP trong Kubernetes"
date: "2024-04-03"
weight: 5
chapter: false
pre: "<b> 1.5 </b>"
---

#### Dịch vụ Kubernetes - ClusterIP
Trong phần này, chúng ta sẽ tìm hiểu về dịch vụ ClusterIP trong Kubernetes.

#### ClusterIP
Trong ngữ cảnh này, ClusterIP tạo ra một địa chỉ IP ảo bên trong cụm để cho phép các dịch vụ khác nhau giao tiếp với nhau như một nhóm máy chủ frontend và một nhóm máy chủ backend.

Cách thức tốt nhất để thiết lập kết nối giữa các dịch vụ hoặc tầng này là gì?

Dịch vụ Kubernetes có thể giúp chúng ta nhóm các pod lại với nhau và cung cấp một giao diện duy nhất để truy cập pod trong một nhóm.

Để tạo một dịch vụ loại ClusterIP:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: back-end
spec:
  type: ClusterIP
  ports:
  - targetPort: 80
    port: 80
  selector:
    app: myapp
    type: back-end
```

Sau đó chạy lệnh sau để tạo dịch vụ:

```bash
$ kubectl create -f service-definition.yaml
```

Để liệt kê các dịch vụ:

```bash
$ kubectl get services
```