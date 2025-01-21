---
title: "Preparing for this lab"
weight: 4
chapter: false
pre: "<b> 2.4 </b>"
---

On the newly created IDE, run this command:

```bash
$ prepare-environment exposing/load-balancer
```

This will make the following changes to your lab environment:
- Creates an IAM role required by the AWS Load Balancer Controller


Kubernetes uses services to expose pods outside of a cluster. One of the most popular ways to use services in AWS is with the `LoadBalancer` type. With a simple YAML file declaring your service name, port, and label selector, the cloud controller will provision a load balancer for you automatically.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: search-svc # tên của dịch vụ của chúng ta
spec:
  type: loadBalancer
  selector:
    app: SearchApp # các pod được triển khai với nhãn app=SearchApp
  ports:
    - port: 80
```

This is great because of how simple it is to put a load balancer in front of your application. The service spec has been extended over the years with annotations and additional configuration. A second option is to use an ingress rule and an ingress controller to route external traffic into Kubernetes pods.

![EKS](../../../images/1/00013.png?featherlight=false&width=60pc)

In this chapter we'll demonstrate how to expose an application running in the EKS cluster to the Internet using a **layer 4 Network Load Balancer**.
