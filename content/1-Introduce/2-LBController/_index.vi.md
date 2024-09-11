---
title: "Bộ điều khiển cân bằng tải"
date: "2024-04-03"
weight: 2
chapter: false
pre: "<b> 1.2 </b>"
---

**AWS Load Balancer Controller** là [bộ điều khiển](https://kubernetes.io/docs/concepts/architecture/controller/) giúp quản lý **Elastic Load Balancer** cho cụm Kubernetes.

Bộ điều khiển có thể cung cấp các tài nguyên sau:

- AWS Application Load Balancer khi bạn tạo Kubernetes `Ingress`.
- AWS Network Load Balancer khi bạn tạo Kubernetes `Service` loại `LoadBalancer`.

Application Load Balancer hoạt động ở `L7` của mô hình OSI, cho phép bạn hiển thị dịch vụ Kubernetes bằng các quy tắc ingress và hỗ trợ lưu lượng truy cập bên ngoài. Network load balancer hoạt động ở `L4` của mô hình OSI, cho phép bạn tận dụng Kubernetes `Services` để hiển thị một tập hợp các pod dưới dạng dịch vụ mạng ứng dụng.

Bộ điều khiển cho phép bạn đơn giản hóa các hoạt động và tiết kiệm chi phí bằng cách chia sẻ Application Load Balancer trên nhiều ứng dụng trong cụm Kubernetes của bạn.

AWS Load Balancer Controller đã được cài đặt trong cụm Kubernetes của chuỗi lab này, vì vậy chúng ta có thể bắt đầu tạo tài nguyên.

{{% notice info %}}
The AWS Load Balancer Controller từng được gọi là AWS ALB Ingress Controller.
{{% /notice %}}