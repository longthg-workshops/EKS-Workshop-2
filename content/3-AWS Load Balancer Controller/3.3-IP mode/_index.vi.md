---
title: "IP mode"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: "<b> 3.3 </b>"
---

#### IP mode


Như đã đề cập trước đó, **NLB** chúng ta đã tạo đang hoạt động ở chế độ "**instance mode**". Chế độ mục tiêu instance hỗ trợ các **pod** đang chạy trên các máy **EC2** của **AWS**. Trong chế độ này, **AWS NLB** gửi lưu lượng đến các instance và `kube-proxy` trên các nút làm việc cá nhân chuyển tiếp nó đến các pod thông qua một hoặc nhiều nút làm việc trong cụm **Kubernetes**.

Bộ điều khiển **Cân bằng Tải AWS** cũng hỗ trợ tạo **NLB** hoạt động ở chế độ "**IP mode**". Trong chế độ này, **AWS NLB** gửi lưu lượng trực tiếp đến các pod **Kubernetes** đằng sau dịch vụ, loại bỏ nhu cầu cho một bước nhảy mạng phụ qua các nút làm việc trong cụm **Kubernetes**. Chế độ mục tiêu IP hỗ trợ các pod đang chạy trên cả các máy **EC2** của **AWS** và **AWS Fargate**.

![EKS](../../../images/1/00014.png?featherlight=false&width=60pc)

Có một số lý do mà chúng ta có thể muốn cấu hình **NLB** để hoạt động ở chế độ mục tiêu IP:

1. Tạo một đường mạng hiệu quả hơn cho các kết nối đến, bỏ qua `kube-proxy` trên nút làm việc **EC2**.
2. Loại bỏ nhu cầu xem xét các khía cạnh như `externalTrafficPolicy` và các tùy chọn cấu hình khác nhau của nó.
3. Một ứng dụng đang chạy trên **Fargate** thay vì **EC2**.

### Tái cấu hình NLB

Hãy tái cấu hình **NLB** của chúng ta để sử dụng chế độ IP và xem xét tác động của nó đối với cơ sở hạ tầng.

Đây là đoạn mã patch chúng ta sẽ áp dụng để tái cấu hình Dịch vụ:

```kustomization
modules/exposing/load-balancer/ip-mode/nlb.yaml
Service/ui-nlb
```

Áp dụng các bản mẫu với **kustomize**:

```bash
$ kubectl apply -k ~/environment/eks-workshop/modules/exposing/load-balancer/ip-mode
```

Sẽ mất vài phút để cấu hình của cân bằng tải được cập nhật. Chạy lệnh sau để đảm bảo chú thích được cập nhật:

```bash
$ kubectl describe service/ui-nlb -n ui
...
Annotations:              service.beta.kubernetes.io/aws-load-balancer-nlb-target-type: ip
...
```

Bạn nên có thể truy cập ứng dụng bằng cùng một URL như trước đó, với **NLB** bây giờ sử dụng chế độ IP để tiết lộ ứng dụng của bạn.

```bash
$ ALB_ARN=$(aws elbv2 describe-load-balancers --query 'LoadBalancers[?contains(LoadBalancerName, `k8s-ui-uinlb`) == `true`].LoadBalancerArn' | jq -r '.[0]')
$ TARGET_GROUP_ARN=$(aws elbv2 describe-target-groups --load-balancer-arn $ALB_ARN | jq -r '.TargetGroups[0].TargetGroupArn')
$ aws elbv2 describe-target-health --target-group-arn $TARGET_GROUP_ARN
{
    "TargetHealthDescriptions": [
        {
            "Target": {
                "Id": "10.42.180.183",
                "Port": 8080,
                "AvailabilityZone": "us-west-2a"
            },
            "HealthCheckPort": "8080",
            "TargetHealth": {
                "State": "initial",
                "Reason": "Elb.RegistrationInProgress",
                "Description": "Target registration is in progress"
            }
        }
    ]
}
```

**Chú ý:** Chúng ta đã chuyển từ 3 mục tiêu mà chúng ta đã quan sát trong phần trước đó thành chỉ một mục tiêu. Tại sao lại như vậy? Thay vì đăng ký các **EC2 instances** trong cụm **EKS** của chúng ta, bộ điều khiển cân bằng tải hiện đang đăng ký các **Pods** cá nhân và gửi lưu lượng trực tiếp, tận dụng **AWS VPC CNI** và sự thật rằng mỗi **Pod** đều có địa chỉ IP VPC cấp đầu.

Hãy mở rộng thành phần ui lên 3 bản sao và xem điều gì xảy ra:

```bash
$ kubectl scale -n ui deployment/ui --replicas=3
$ kubectl wait --for=condition=Ready pod -n ui -l app.kubernetes.io/name=ui --timeout=60s
```

Bây giờ hãy kiểm tra lại các mục tiêu của cân bằng tải:

```bash
$ ALB_ARN=$(aws elbv2 describe-load-balancers --query 'LoadBalancers[?contains(LoadBalancerName, `k8s-ui-uinlb`) == `true`].LoadBalancerArn' | jq -r '.[0]')
$ TARGET_GROUP_ARN=$(aws elbv2 describe-target-groups --load-balancer-arn $ALB_ARN | jq -r '.TargetGroups[0].TargetGroupArn')
$ aws elbv2 describe-target-health --target-group-arn $TARGET_GROUP_ARN
{
    "TargetHealthDescriptions": [
        {
            "Target": {
                "Id": "10.42.180.181",
                "Port": 8080,
                "AvailabilityZone": "us

-west-2c"
            },
            "HealthCheckPort": "8080",
            "TargetHealth": {
                "State": "initial",
                "Reason": "Elb.RegistrationInProgress",
                "Description": "Target registration is in progress"
            }
        },
        {
            "Target": {
                "Id": "10.42.140.129",
                "Port": 8080,
                "AvailabilityZone": "us-west-2a"
            },
            "HealthCheckPort": "8080",
            "TargetHealth": {
                "State": "healthy"
            }
        },
        {
            "Target": {
                "Id": "10.42.105.38",
                "Port": 8080,
                "AvailabilityZone": "us-west-2a"
            },
            "HealthCheckPort": "8080",
            "TargetHealth": {
                "State": "initial",
                "Reason": "Elb.RegistrationInProgress",
                "Description": "Target registration is in progress"
            }
        }
    ]
}
```

Như dự kiến, bây giờ chúng ta có 3 mục tiêu, khớp với số lượng bản sao trong Deployment ui.

Nếu bạn muốn chờ đợi để đảm bảo ứng dụng vẫn hoạt động như trước, hãy chạy lệnh sau. Nếu không, bạn có thể tiếp tục sang module tiếp theo.

```bash timeout=240
$ wait-for-lb $(kubectl get service -n ui ui-nlb -o jsonpath="{.status.loadBalancer.ingress[*].hostname}{'\n'}")
```