---
title: "Tạo Ingress"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: "<b> 4.2 </b>"
---

#### Tạo Ingress

Hãy tạo một tài nguyên Ingress với bản mô tả sau:

```file
manifests/modules/exposing/ingress/creating-ingress/ingress.yaml
```

Điều này sẽ khiến AWS Load Balancer Controller triển khai một Application Load Balancer và cấu hình nó để định tuyến lưu lượng đến các Pod cho ứng dụng `ui`.

```bash timeout=180 hook=add-ingress hookTimeout=430
$ kubectl apply -k ~/environment/eks-workshop/modules/exposing/ingress/creating-ingress
```

Hãy kiểm tra tài nguyên Ingress đã được tạo:

```bash
$ kubectl get ingress ui -n ui
NAME   CLASS   HOSTS   ADDRESS                                            PORTS   AGE
ui     alb     *       k8s-ui-ui-1268651632.us-west-2.elb.amazonaws.com   80      15s
```

ALB sẽ mất vài phút để triển khai và đăng ký các mục tiêu của nó, vì vậy hãy dành một chút thời gian để xem xét cẩn thận về ALB được triển khai cho Ingress này để xem cách nó được cấu hình:

```bash
$ aws elbv2 describe-load-balancers --query 'LoadBalancers[?contains(LoadBalancerName, `k8s-ui-ui`) == `true`]'
[
    {
        "LoadBalancerArn": "arn:aws:elasticloadbalancing:us-west-2:1234567890:loadbalancer/app/k8s-ui-ui-cb8129ddff/f62a7bc03db28e7c",
        "DNSName": "k8s-ui-ui-cb8129ddff-1888909706.us-west-2.elb.amazonaws.com",
        "CanonicalHostedZoneId": "Z1H1FL5HABSF5",
        "CreatedTime": "2022-09-30T03:40:00.950000+00:00",
        "LoadBalancerName": "k8s-ui-ui-cb8129ddff",
        "Scheme": "internet-facing",
        "VpcId": "vpc-0851f873025a2ece5",
        "State": {
            "Code": "active"
        },
        "Type": "application",
        "AvailabilityZones": [
            {
                "ZoneName": "us-west-2b",
                "SubnetId": "subnet-00415f527bbbd999b",
                "LoadBalancerAddresses": []
            },
            {
                "ZoneName": "us-west-2a",
                "SubnetId": "subnet-0264d4b9985bd8691",
                "LoadBalancerAddresses": []
            },
            {
                "ZoneName": "us-west-2c",
                "SubnetId": "subnet-05cda6deed7f3da65",
                "LoadBalancerAddresses": []
            }
        ],
        "SecurityGroups": [
            "sg-0f8e704ee37512eb2",
            "sg-02af06ec605ef8777"
        ],
        "IpAddressType": "ipv4"
    }
]
```

Điều này nói cho chúng ta điều gì?

- ALB có thể truy cập qua public internet
- Nó sử dụng các public subnet trong VPC của chúng tôi

Kiểm tra các mục tiêu trong nhóm mục tiêu được tạo ra bởi bộ điều khiển:

```bash
$ ALB_ARN=$(aws elbv2 describe-load-balancers --query 'LoadBalancers[?contains(LoadBalancerName, `k8s-ui-ui`) == `true`].LoadBalancerArn' | jq -r '.[0]')
$ TARGET_GROUP_ARN=$(aws elbv2 describe-target-groups --load-balancer-arn $ALB_ARN | jq -r '.TargetGroups[0].TargetGroupArn')
$ aws elbv2 describe-target-health --target-group-arn $TARGET_GROUP_ARN
{
    "TargetHealthDescriptions": [
        {
            "Target": {
                "Id": "10.42.180.183",
                "Port": 8080,
                "AvailabilityZone": "us-west-2c"
            },
            "HealthCheckPort": "8080",
            "TargetHealth": {
                "State": "healthy"
            }
        }
    ]
}
```

Vì chúng tôi đã chỉ định sử dụng chế độ IP trong đối tượng Ingress của chúng tôi, mục tiêu được đăng ký bằng địa chỉ IP của pod `ui` và cổng mà nó phục vụ lưu lượng.

Bạn cũng có thể kiểm tra ALB và các nhóm mục tiêu của nó trong bảng điều khiển bằng cách nhấp vào liên kết này:

[https://console.aws.amazon.com/ec2/home#LoadBalancers:tag:ingress.k8s.aws/stack=ui/ui;sort=loadBalancerName](https://console.aws.amazon.com/ec2/home#LoadBalancers:tag:ingress.k8s.aws/stack=ui/ui;sort=loadBalancerName)

Lấy URL từ tài nguyên Ingress:

```bash
$ kubectl get ingress -n ui ui -o jsonpath="{.status.loadBalancer.ingress[*].hostname}{'\n'}"
k8s-ui-uinlb-a9797f0f61.elb.us-west-2.amazonaws.com
```

Để chờ đến khi bộ cân bằng tải hoàn tất triển khai, bạn có thể chạy lệnh này:

```bash
$ wait-for-lb $(kubectl get ingress -n ui ui -o jsonpath="{.status.loadBalancer.ingress[*].hostname}{'\n'}")
```

Và truy cập vào trình duyệt web của bạn. Bạn sẽ thấy giao diện người dùng từ cửa hàng web được hiển thị và sẽ có thể di chuyển xung quanh trang web như một người dùng.