---
title: "Giới thiệu"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: "<b> 3.1 </b>"
---

#### Giới thiệu
Đầu tiên, chúng ta cài đặt AWS Load Balancer Controller bằng Helm:
```bash
$ helm repo add eks-charts https://aws.github.io/eks-charts
$ helm upgrade --install aws-load-balancer-controller eks-charts/aws-load-balancer-controller \
    --version "${LBC_CHART_VERSION}" \
    --namespace "kube-system" \
    --set "clusterName=${EKS_CLUSTER_NAME}" \
    --set "serviceAccount.name=aws-load-balancer-controller-sa" \
    --set "serviceAccount.annotations.eks\\.amazonaws\\.com/role-arn"="$LBC_ROLE_ARN" \
    --wait
```

Chúng ta cần xác nhận rằng các vi dịch vụ của chúng ta chỉ có thể truy cập nội bộ bằng cách nhìn vào các tài nguyên **current Service** trong cụm:

```bash
$ kubectl get svc -l app.kubernetes.io/created-by=eks-workshop -A
NAMESPACE   NAME             TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                                 AGE
assets      assets           ClusterIP   172.20.119.246   <none>        80/TCP                                  1h
carts       carts            ClusterIP   172.20.180.149   <none>        80/TCP                                  1h
carts       carts-dynamodb   ClusterIP   172.20.92.137    <none>        8000/TCP                                1h
catalog     catalog          ClusterIP   172.20.83.84     <none>        80/TCP                                  1h
catalog     catalog-mysql    ClusterIP   172.20.181.252   <none>        3306/TCP                                1h
checkout    checkout         ClusterIP   172.20.77.176    <none>        80/TCP                                  1h
checkout    checkout-redis   ClusterIP   172.20.32.208    <none>        6379/TCP                                1h
orders      orders           ClusterIP   172.20.146.72    <none>        80/TCP                                  1h
orders      orders-mysql     ClusterIP   172.20.54.235    <none>        3306/TCP                                1h
rabbitmq    rabbitmq         ClusterIP   172.20.107.54    <none>        5672/TCP,4369/TCP,25672/TCP,15672/TCP   1h
ui          ui               ClusterIP   172.20.62.119    <none>        80/TCP                                  1h
```

Tất cả các thành phần ứng dụng của chúng ta hiện đang sử dụng các Service **ClusterIP**, chỉ cho phép truy cập đến các công việc khác trong cùng một cụm **Kubernetes**. Để người dùng có thể truy cập ứng dụng của chúng ta, chúng ta cần phải hiển thị ứng dụng `ui`, và trong ví dụ này, chúng ta sẽ làm điều đó bằng cách sử dụng **Kubernetes services** loại **LoadBalancer**.

Trước tiên, hãy xem xét cẩn thận thông số kỹ thuật hiện tại của **Service** cho thành phần `ui`:

```bash
$ kubectl -n ui describe service ui
Name:              ui
Namespace:         ui
Labels:            app.kubernetes.io/component=service
                   app.kubernetes.io/created-by: eks-workshop
                   app.kubernetes.io/instance=ui
                   app.kubernetes.io/managed-by=Helm
                   app.kubernetes.io/name=ui
                   helm.sh/chart=ui-0.0.1
Annotations:       <none>
Selector:          app.kubernetes.io/component=service,app.kubernetes.io/instance=ui,app.kubernetes.io/name=ui
Type:              ClusterIP
IP Family Policy:  SingleStack
IP Families:       IPv4
IP:                172.20.62.119
IPs:               172.20.62.119
Port:              http  80/TCP
TargetPort:        http/TCP
Endpoints:         10.42.105.38:8080
Session Affinity:  None
Events:            <none>
```

Như chúng ta đã thấy trước đó, hiện đang sử dụng loại **ClusterIP** và nhiệm vụ của chúng ta trong module này là thay đổi điều này để giao diện người dùng của cửa hàng bán lẻ có thể truy cập thông qua **Public internet**.