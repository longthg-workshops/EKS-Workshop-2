---
title: "Ingress"
date: "2024-04-03"
weight: 4
chapter: false
pre: "<b> 4. </b>"
---

#### Chuẩn bị môi trường cho phần này:

```bash
$ prepare-environment exposing/ingress
```

Thao tác này sẽ thực hiện các thay đổi sau đây trong môi trường thí nghiệm của bạn:

- Cài đặt **AWS Load Balancer Controller** trong cụm Amazon EKS

**Kubernetes Ingress** là một tài nguyên API cho phép bạn quản lý truy cập HTTP(S) bên ngoài hoặc bên trong đến các dịch vụ Kubernetes đang chạy trong một cụm. **Amazon Elastic Load Balancing Application Load Balancer (ALB)** là một dịch vụ AWS phổ biến load balance lưu lượng vào đến lớp ứng dụng (lớp 7) qua nhiều mục tiêu, chẳng hạn như các instance **Amazon EC2**, trong một khu vực. ALB hỗ trợ nhiều tính năng bao gồm định tuyến dựa trên host hoặc đường dẫn, kết thúc TLS (Transport Layer Security), **WebSockets**, **HTTP/2**, tích hợp **AWS WAF (Web Application Firewall)**, ghi nhật ký truy cập tích hợp và kiểm tra sức khỏe.

Trong bài tập thực hành này, chúng ta sẽ hiển thị ứng dụng mẫu của chúng ta bằng cách sử dụng một ALB với mô hình ingress của Kubernetes.
