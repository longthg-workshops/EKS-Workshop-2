---
title: "Tạo load balancer"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: "<b> 3.2 </b>"
---

#### Tạo load balancer


Trong môi trường **AWS** và **Kubernetes**, việc triển khai một **load balancer** là một phần quan trọng của quá trình triển khai ứng dụng. **Load balancer** giúp phân phối khối lượng công việc và cung cấp sự ổn định cho các ứng dụng đang chạy trên các **cluster**. Dưới đây là hướng dẫn chi tiết để tạo một **load balancer**.

**_~/environment/eks-workshop/modules/exposing/load-balancer/nlb/nlb.yaml_**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: ui-nlb
  annotations:
    service.beta.kubernetes.io/aws-load-balancer-type: external
    service.beta.kubernetes.io/aws-load-balancer-scheme: internet-facing
    service.beta.kubernetes.io/aws-load-balancer-nlb-target-type: instance
  namespace: ui
spec:
  type: LoadBalancer
  ports:
    - port: 80
      targetPort: 8080
      name: http
  selector:
    app.kubernetes.io/name: ui
    app.kubernetes.io/instance: ui
    app.kubernetes.io/component: service
```

Dịch vụ này sẽ tạo ra một **Trình cân bằng tải Mạng** lắng nghe trên cổng 80 và chuyển tiếp kết nối đến các **Pod** `ui` trên cổng 8080. Một **NLB** là một trình cân bằng tải tầng 4 hoạt động ở tầng TCP trong trường hợp của chúng ta.

```bash
$ kubectl apply -k ~/environment/eks-workshop/modules/exposing/load-balancer/nlb
```

Hãy kiểm tra lại tài nguyên **Dịch vụ** cho ứng dụng `ui`:

```bash
$ kubectl get service -n ui
```

Chúng ta thấy hai tài nguyên riêng biệt, với mục nhập `ui-nlb` mới có kiểu `LoadBalancer`. Quan trọng nhất là lưu ý rằng nó có giá trị "địa chỉ IP bên ngoài", đây là mục nhập **DNS** có thể được sử dụng để truy cập ứng dụng của chúng ta từ bên ngoài cụm **Kubernetes**.

**NLB** sẽ mất vài phút để triển khai và đăng ký các mục tiêu của nó nên hãy dành thời gian để kiểm tra các tài nguyên **trình cân bằng tải** mà bộ điều khiển đã tạo ra.

Trước tiên, hãy xem xét trình cân bằng tải:

```bash
$ aws elbv2 describe-load-balancers --query 'LoadBalancers[?contains(LoadBalancerName, `k8s-ui-uinlb`) == `true`]'
[
    {
        "LoadBalancerArn": "arn:aws:elasticloadbalancing:us-west-2:1234567890:loadbalancer/net/k8s-ui-uinlb-e1c1ebaeb4/28a0d1a388d43825",
        "DNSName": "k8s-ui-uinlb-e1c1ebaeb4-28a0d1a388d43825.elb.us-west-2.amazonaws.com",
        "CanonicalHostedZoneId": "Z18D5FSROUN65G",
        "CreatedTime": "2022-11-17T04:47:30.516000+00:00",
        "LoadBalancerName": "k8s-ui-uinlb-e1c1ebaeb4",
        "Scheme": "internet-facing",
        "VpcId": "vpc-00be6fc048a845469",
        "State": {
            "Code": "active"
        },
        "Type": "network",
        "AvailabilityZones": [
            {
                "ZoneName": "us-west-2c",
                "SubnetId": "subnet-0a2de0809b8ee4e39",
                "LoadBalancerAddresses": []
            },
            {
                "ZoneName": "us-west-2a",
                "SubnetId": "subnet-0ff71604f5b58b2ba",
                "LoadBalancerAddresses": []
            },
            {
                "ZoneName": "us-west-2b",
                "SubnetId": "subnet-0c584c4c6a831e273",
                "LoadBalancerAddresses": []
            }
        ],
        "IpAddressType": "ipv4"
    }
]
```

Điều này nói với chúng ta điều gì?

- **NLB** có thể truy cập qua public internet
- Nó sử dụng các public subnet trong **VPC** của chúng ta

Chúng ta cũng có thể kiểm tra các mục tiêu trong nhóm mục tiêu được tạo ra bởi bộ điều khiển:

```bash
$ ALB_ARN=$(aws elbv2 describe-load-balancers --query 'LoadBalancers[?contains(LoadBalancerName, `k8s-ui-uinlb`) == `true`].LoadBalancerArn' | jq -r '.[0]')
$ TARGET_GROUP_ARN=$(aws elbv2 describe-target-groups --load-balancer-arn $ALB_ARN | jq -r '.TargetGroups[0].TargetGroupArn')
$ aws elbv2 describe-target-health --target-group-arn $TARGET_GROUP_ARN
{
    "TargetHealthDescriptions": [
        {
            "Target": {
                "Id": "i-06a12e62c14e0c39a",
                "Port": 31338
            },
            "HealthCheckPort": "31338",
            "TargetHealth": {
                "State": "healthy"
            }
        },
        {
            "Target": {
                "Id": "i-088e21d0af0f2890c",
                "Port": 31338
            },
            "HealthCheckPort": "31338",
            "TargetHealth": {
                "State": "healthy"
            }
        },
        {
            "Target": {
                "Id": "i-0fe2202d18299816f",
                "Port": 31338
            },
            "HealthCheckPort": "31338",
            "TargetHealth": {
                "State": "healthy"
            }
        }
    ]
}
```

Đầu ra trên cho thấy chúng ta có 3 mục tiêu đã đăng ký vào **trình cân bằng tải** bằng các **ID instance EC2** (`i-`) mỗi cái trên cùng một cổng. Lý do cho điều này là mặc định, **Bộ Điều khiển Trình Cân bằng Tải AWS** hoạt động ở "chế độ instance", chuyển hướng lưu lượng đến các nút worker trong **cụm EKS** và cho phép `kube-proxy` chuyển tiếp lưu lượng đến các **Pod** cá nhân.

Bạn cũng có thể kiểm tra **NLB** trong bảng điều khiển bằng cách nhấp vào [liên kết này](https://console.aws.amazon.com/ec2/home#LoadBalancers:tag:service.k8s.aws/stack=ui/ui-nlb;sort=loadBalancerName).

Lấy liên kết từ tài nguyên Service:

```bash
$ kubectl get service -n ui ui-nlb -o jsonpath="{.status.loadBalancer.ingress[*].hostname}{'\n'}"
k8s-ui-uinlb-a9797f0f61.elb.us-west-2.amazonaws.com
```

Để chờ đến khi việc cấp phát bộ cân bằng tải hoàn thành, bạn có thể chạy lệnh sau:

```bash
$ wait-for-lb $(kubectl get service -n ui ui-nlb -o jsonpath="{.status.loadBalancer.ingress[*].hostname}{'\n'}")
```

Bây giờ ứng dụng của chúng ta đã được đưa ra thế giới bên ngoài, hãy thử truy cập bằng cách dán URL đó vào trình duyệt web của bạn. Bạn sẽ thấy giao diện người dùng từ cửa hàng web được hiển thị và có thể điều hướng xung quanh trang web với tư cách là người dùng.