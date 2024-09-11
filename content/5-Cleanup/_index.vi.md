---
title: "Dọn dẹp tài nguyên"
date: "`r Sys.Date()`"
weight: 5
chapter: false
pre: "<b> 5. </b>"
---

Trước khi xoá CloudFormation Stack của workshop, hãy đảm bảo rằng bạn đã xoá các tài nguyên lab và cụm EKS như được hướng dẫn ở phần [2.1](../../2-Prerequiste/2.1-eksctl/) hoặc [2.2](../../2-Prerequiste/2.2-terraform/) (tuỳ theo hình thức triển khai tài nguyên `eksctl` và `Terraform` tương ứng).

Sau khi thực hiện xong bước trên, chạy lệnh sau trên giao diện dòng lệnh CloudShell để xoá tài nguyên IDE:

```bash
aws cloudformation delete-stack --stack-name eks-workshop-ide
```