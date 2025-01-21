---
title: "AWS Load Balancer Controller"
weight: 3
chapter: false
pre: "<b> 3. </b>"
---

#### Quản lý AWS Load Balancer Controller trong Kubernetes

**AWS Load Balancer Controller** là một [controller](https://kubernetes.io/docs/concepts/architecture/controller/) giúp quản lý Elastic Load Balancers cho một cluster Kubernetes.

Controller này có thể cung cấp các tài nguyên sau:

- Một AWS Application Load Balancer khi bạn tạo một `Ingress` trong Kubernetes.
- Một AWS Network Load Balancer khi bạn tạo một `Service` trong Kubernetes với loại `LoadBalancer`.

Các Application Load Balancers hoạt động ở tầng `L7` của mô hình OSI, cho phép bạn phơi bày dịch vụ Kubernetes bằng các quy tắc ingress và hỗ trợ lưu lượng có chiều ra ngoài. Network Load Balancers hoạt động ở tầng `L4` của mô hình OSI, cho phép bạn tận dụng các `Services` Kubernetes để phơi bày một tập hợp các pods như một dịch vụ mạng ứng dụng.

Controller này giúp bạn đơn giản hóa hoạt động và tiết kiệm chi phí bằng cách chia sẻ một Application Load Balancer qua nhiều ứng dụng trong cluster Kubernetes của bạn.

AWS Load Balancer Controller đã được cài đặt sẵn trong cluster của chúng ta, vì vậy chúng ta có thể bắt đầu tạo các tài nguyên.

{{% notice info %}}
AWS Load Balancer Controller trước đây được gọi là AWS ALB Ingress Controller.
{{% /notice %}}