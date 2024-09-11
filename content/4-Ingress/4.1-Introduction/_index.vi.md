---
title: "Giới thiệu"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: "<b> 4.1 </b>"
---
Ư
Hiện tại, trong cụm của chúng ta không có tài nguyên **Ingress**, bạn có thể kiểm tra bằng lệnh sau:

```bash expectError=true
$ kubectl get ingress -n ui
No resources found in ui namespace.
```

Cũng không có tài nguyên **Service** loại `LoadBalancer`, bạn có thể xác nhận bằng lệnh sau:

```bash
$ kubectl get svc -n ui
NAME   TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
ui     ClusterIP   10.100.221.103   <none>        80/TCP    29m
```

