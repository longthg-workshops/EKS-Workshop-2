---
title: "Mô hình nhiều Ingress"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: "<b> 4.3 </b>"
---

#### Multiple Ingress pattern

Trong một EKS cluster, việc tận dụng nhiều đối tượng Ingress là điều phổ biến, ví dụ để tiếp cận nhiều workloads khác nhau. Theo mặc định, mỗi Ingress sẽ dẫn đến việc tạo ra một ALB riêng biệt, nhưng chúng ta có thể tận dụng tính năng IngressGroup để nhóm nhiều tài nguyên Ingress lại với nhau. Controller sẽ tự động hợp nhất các quy tắc Ingress cho tất cả các Ingress trong IngressGroup và hỗ trợ chúng với một ALB duy nhất. Ngoài ra, hầu hết các chú thích được định nghĩa trên một Ingress chỉ áp dụng cho các đường dẫn được xác định bởi Ingress đó.

Trong ví dụ này, chúng ta sẽ tiếp cận API `catalog` thông qua cùng một ALB như thành phần `ui`, tận dụng định tuyến dựa trên đường dẫn để chuyển hướng các yêu cầu đến dịch vụ Kubernetes phù hợp. Hãy kiểm tra xem chúng ta đã có thể truy cập API catalog chưa:

```bash expectError=true
$ ADDRESS=$(kubectl get ingress -n ui ui -o jsonpath="{.status.loadBalancer.ingress[*].hostname}{'\n'}")
$ curl $ADDRESS/catalogue
```

Điều đầu tiên chúng ta sẽ làm là tạo lại Ingress cho thành phần `ui`, thêm chú thích `alb.ingress.kubernetes.io/group.name`:

```file
manifests/modules/exposing/ingress/multiple-ingress/ingress-ui.yaml
```

Bây giờ, hãy tạo một Ingress riêng cho thành phần `catalog` cũng tận dụng `group.name` tương tự:

```file
manifests/modules/exposing/ingress/multiple-ingress/ingress-catalog.yaml
```

Ingress này cũng cấu hình quy tắc để định tuyến các yêu cầu có tiền tố `/catalogue` đến thành phần `catalog`.

Áp dụng các tài nguyên này vào cluster:

```bash timeout=180 hook=add-ingress hookTimeout=430
$ kubectl apply -k ~/environment/eks-workshop/modules/exposing/ingress/multiple-ingress
```

Bây giờ chúng ta sẽ có hai đối tượng Ingress riêng biệt trong cluster của chúng ta:

```bash
$ kubectl get ingress -l app.kubernetes.io/created-by=eks-workshop -A
NAMESPACE   NAME      CLASS   HOSTS   ADDRESS                                                              PORTS   AGE
catalog     catalog   alb     *       k8s-retailappgroup-2c24c1c4bc-17962260.us-west-2.elb.amazonaws.com   80      2m21s
ui          ui        alb     *       k8s-retailappgroup-2c24c1c4bc-17962260.us-west-2.elb.amazonaws.com   80      2m21s
```

Chú ý rằng `ADDRESS` của cả hai đều là cùng một URL, điều này là vì cả hai đối tượng Ingress này đang được nhóm lại với nhau sau một ALB duy nhất.

Chúng ta có thể xem Listener của ALB để hiểu cách hoạt động này:

```bash
$ ALB_ARN=$(aws elbv2 describe-load-balancers --query 'LoadBalancers[?contains(LoadBalancerName, `k8s-retailappgroup`) == `true`].LoadBalancerArn' | jq -r '.[0]')
$ LISTENER_ARN=$(aws elbv2 describe-listeners --load-balancer-arn $ALB_ARN | jq -r '.Listeners[0].ListenerArn')
$ aws elbv2 describe-rules --listener-arn $LISTENER_ARN
```

Kết quả của lệnh này sẽ minh họa rằng:

- Các yêu cầu có tiền tố đường dẫn `/catalogue` sẽ được gửi đến một nhóm mục tiêu cho dịch vụ catalog
- Tất cả mọi thứ khác sẽ được gửi đến một nhóm mục tiêu cho dịch vụ ui
- Là một lựa chọn mặc định backup, có một 404 cho bất kỳ yêu cầu nào rơi vào những khe hở

Bạn cũng có thể kiểm tra cấu hình ALB mới trong AWS console:

[https://console.aws.amazon.com/ec2/home#LoadBalancers:tag:ingress.k8s.aws/stack=retail-app-group;sort=loadBalancerName](https://console.aws.amazon.com/ec2/home#LoadBalancers:tag:ingress.k8s.aws/stack=retail-app-group;sort=loadBalancerName)

Để chờ cho đến khi load balancer hoàn thành triển khai, bạn có thể chạy lệnh này:

```bash
$ wait-for-lb $(kubectl get ingress -n ui ui -o jsonpath="{.status.loadBalancer.ingress[*].hostname}{'\n'}")
```

Thử truy cập URL Ingress mới trong trình duyệt như trước để kiểm tra giao diện web vẫn hoạt động:

```bash
$ kubectl get ingress -n ui ui -o jsonpath="{.status.loadBalancer.ingress[*].hostname}{'\n'}"
k8s-ui-uinlb-a9797f0f61.elb.us-west-2.amazonaws.com
```

Bây giờ hãy thử truy cập vào đường dẫn cụ thể mà chúng ta đã chuyển hướng đến dịch vụ catalog:

```bash
ADDRESS=$(kubectl get ingress -n ui ui -o jsonpath="{.status.loadBalancer.ingress[*].hostname}{'\n'}")
curl $ADDRESS/catalogue | jq .

```

Bạn sẽ nhận lại một tải dữ liệu JSON từ dịch vụ catalog, chứng tỏ chúng ta đã có thể tiếp cận nhiều dịch vụ Kubernetes qua cùng một ALB.