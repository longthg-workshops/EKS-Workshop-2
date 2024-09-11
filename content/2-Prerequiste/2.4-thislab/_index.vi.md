---
title: "Chuẩn bị môi trường cho Lab này"
date: "2024-04-03"
weight: 4
chapter: false
pre: "<b> 2.4 </b>"
---

Trên môi trường IDE đã tạo, chạy lệnh sau:

```bash
$ prepare-environment exposing/load-balancer
```

Thao tác này sẽ thực hiện các thay đổi sau vào môi trường lab của bạn:
- Cài đặt **AWS Load Balancer Controller** trong cụm **Amazon EKS**


**Kubernetes** sử dụng dịch vụ để phơi bày các pod ra bên ngoài một cụm. Một trong những cách phổ biến nhất để sử dụng dịch vụ trong **AWS** là với kiểu `LoadBalancer`. Với một tệp **YAML** đơn giản khai báo tên dịch vụ, cổng và bộ chọn nhãn, bộ điều khiển đám mây sẽ tự động cung cấp một bộ cân bằng tải cho bạn.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: search-svc # tên của dịch vụ của chúng ta
spec:
  type: loadBalancer
  selector:
    app: SearchApp # các pod được triển khai với nhãn app=SearchApp
  ports:
    - port: 80
```

Điều này rất tuyệt vì tính đơn giản của việc đặt một bộ cân bằng tải trước ứng dụng của bạn. Đặc tả dịch vụ đã được mở rộng qua các năm với các chú thích và cấu hình bổ sung. Một lựa chọn thứ hai là sử dụng quy tắc nhập và một bộ điều khiển nhập để định tuyến lưu lượng bên ngoài vào các pod **Kubernetes**.

![EKS](../../../images/1/00013.png?featherlight=false&width=60pc)

Trong chương này, chúng ta sẽ thể hiện cách phơi bày một ứng dụng đang chạy trong cụm **EKS** ra Internet bằng cách sử dụng một **Bộ cân bằng Tải Mạng** ở tầng 4.
