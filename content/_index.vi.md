---
title: "Hiển thị dịch vụ của bạn trên Amazon EKS"
date: "`r Sys.Date()`"
weight: 1
chapter: false
---

# Cân bằng tải và Ingress trên Amazon EKS

### Kubernetes

Kubernetes là một **nền tảng mã nguồn mở**, linh hoạt, có khả năng mở rộng, phục vụ việc quản lý các ứng dụng được đóng gói và các dịch vụ liên quan, giúp việc cấu hình và tự động hóa quá trình triển khai ứng dụng trở nên thuận tiện hơn. Được biết đến như một hệ sinh thái lớn và phát triển nhanh chóng, Kubernetes cung cấp sự hỗ trợ rộng rãi qua các dịch vụ và công cụ đa dạng.

Tên **Kubernetes** bắt nguồn từ tiếng Hy Lạp, nghĩa là người lái tàu hoặc hoa tiêu. Kubernetes được Google công bố mã nguồn vào năm 2014, dựa trên gần một thập kỷ kinh nghiệm quản lý workload lớn trong thực tế của Google, kết hợp với các ý tưởng và **best practices** từ cộng đồng.

#### Tại sao bạn cần Kubernetes và nó có thể làm gì?

Container là phương tiện hiệu quả để đóng góp và chạy ứng dụng của bạn. Trong môi trường sản xuất, cần có cơ chế quản lý các container một cách hiệu quả, đảm bảo không có downtime. Kubernetes giúp quản lý các hệ thống phân tán mạnh mẽ, tự động hóa việc nhân rộng, cung cấp các mẫu triển khai và nhiều hơn nữa.

Kubernetes mang lại:

- **Phát hiện dịch vụ và cân bằng tải**
- **Điều phối bộ nhớ**
- **Tự động rollouts và rollbacks**
- **Đóng gói tự động**
- **Tự phục hồi**
- **Quản lý cấu hình và bảo mật**

### Amazon Elastic Kubernetes Service (EKS)
Amazon Elastic Kubernetes Service (Amazon EKS) là một dịch vụ được quản lý giúp loại bỏ nhu cầu cài đặt, vận hành và bảo trì lớp điều khiển Kubernetes của riêng bạn trên Amazon Web Services (AWS).

### Các tính năng của Amazon EKS
Sau đây là các tính năng chính của Amazon EKS:

#### Xây dựng mạng và xác thực an toàn
Amazon EKS tích hợp khối công việc của bạn trên Kubernetes với các dịch vụ mạng và bảo mật của AWS. Nó cũng tích hợp với AWS Identity and Access Management (IAM) để cung cấp xác thực cho các cụm Kubernetes của bạn.

#### Dễ dàng mở rộng cụm
Amazon EKS cho phép bạn dễ dàng chỉnh quy mô các cụm Kubernetes của mình lên và xuống dựa trên nhu cầu của khối lượng công việc của bạn. Amazon EKS hỗ trợ tự động mở rộng Pod theo chiều ngang dựa trên CPU hoặc số liệu tùy chỉnh và tự động mở rộng cụm dựa trên nhu cầu của toàn bộ khối lượng công việc.

#### Trải nghiệm Kubernetes được quản lý bởi AWS
Bạn có thể thực hiện các thay đổi đối với các cụm Kubernetes của mình bằng eksctl, AWS Management Console, AWS Command Line Interface (AWS CLI), API, kubectl và Terraform.

#### Tính khả dụng cao
Amazon EKS cung cấp tính khả dụng cao cho lớp điều khiển của bạn trên nhiều Vùng khả dụng.

#### Tích hợp với các dịch vụ AWS
Amazon EKS tích hợp với các dịch vụ AWS khác, cung cấp nền tảng toàn diện để triển khai và quản lý các ứng dụng được chứa trong container của bạn. Bạn cũng có thể dễ dàng khắc phục sự cố khối lượng công việc Kubernetes của mình hơn bằng nhiều công cụ quan sát khác nhau.