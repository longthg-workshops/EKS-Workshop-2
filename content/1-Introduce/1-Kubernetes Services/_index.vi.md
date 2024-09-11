---
title: "Dịch vụ Kubernetes"
date: "2024-04-03"
weight: 1
chapter: false
pre: "<b> 1.1 </b>"
---

### Dịch vụ (Services)

Dịch vụ **Kubernetes** cho phép giao tiếp giữa các thành phần khác nhau trong và ngoài ứng dụng. Dịch vụ giúp chúng ta nhóm các pod lại với nhau và cung cấp một giao diện duy nhất để truy cập pod trong một nhóm.

Hãy xem qua một số khía cạnh khác về mạng lưới.

### Giao Tiếp Bên Ngoài (External Communication)

Làm thế nào chúng ta, như một người dùng bên ngoài, có thể truy cập trang web?

- Từ node (Có thể tiếp cận ứng dụng như mong đợi)
- Từ thế giới bên ngoài (Điều này là mong đợi của chúng ta, mà không có gì ở giữa thì sẽ không thể tiếp cận được ứng dụng)

### Các Loại Dịch Vụ (Service Types)
Có 4 loại dịch vụ trong **Kubernetes**:

![SvcTypes](../../../images/1/1/0001.png)

#### 1. NodePort

Nơi mà dịch vụ làm cho một cổng nội bộ có thể tiếp cận trên một cổng trên **node**.

Khai báo dịch vụ dạng NodePort:

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

```bash
$ curl http://<ip-address>:<port>
```

#### 2. ClusterIP

Trong trường hợp này, dịch vụ tạo một **Địa chỉ IP** ảo trong cụm để cho phép giao tiếp giữa các dịch vụ khác nhau như một bộ máy chủ frontend và một bộ máy chủ backend.

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

#### 3. LoadBalancer

Nơi mà dịch vụ cung cấp một trình cân bằng tải cho ứng dụng của chúng ta, dựa vào các nhà cung cấp đám mây được hỗ trợ. Với một tệp YAML đơn giản khai báo tên dịch vụ, cổng và bộ chọn nhãn của bạn, bộ điều khiển đám mây sẽ tự động cung cấp bộ cân bằng tải cho bạn.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: search-svc
  type: loadBalancer
  selector:
    app: SearchApp # pod có nhãn app=SearchApp
  ports:
    - port: 80
```
Thông số kỹ thuật dịch vụ đã được mở rộng qua nhiều năm với các chú thích và cấu hình bổ sung. Một lựa chọn khác là sử dụng quy tắc ingress và bộ điều khiển ingress để định tuyến lưu lượng truy cập bên ngoài vào các pod Kubernetes.

#### 4. ExternalName
An ExternalName Service is a special case of Service that does not have selectors and uses DNS names instead, e.g.
```yaml
apiversion: v1
kind: Service
metadata:
  name: my-database-svc
  namespace: prod
spec:
  type: ExternalName
  externalName: my.database.example.com
```
When looking up the service my-database-svc.prod.svc.cluster.local, the cluster DNS Service returns a CNAME record for my.database.example.com.