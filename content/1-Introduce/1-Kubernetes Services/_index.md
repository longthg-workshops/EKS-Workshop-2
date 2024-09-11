---
title: "Kubernetes Services"
date: "2024-04-03"
weight: 1
chapter: false
pre: "<b> 1.1 </b>"
---

### Services
- Kubernetes Services enables communication between various components within and outside of the application. A service can help us group the pods together and provide a single interface to access the pod in a group.

### External Communication

- How do we as an **`external user`** access the **`web page`**?

- From the node (Able to reach the application as expected)

- From outside world (This should be our expectation, without something in the middle it will not reach the application)

### Service Types

There are 4 types of service types in kubernetes.

![SvcTypes](../../images/1/1/0001.png)

#### 1. NodePort
- Where the service makes an internal port accessible on a port on the NODE.
```
apiVersion: v1
kind: Service
metadata:
name: myapp-service
spec:
types: NodePort
ports:
- targetPort: 80
    port: 80
    nodePort: 30008
```

To connect the service to the pod
```
apiVersion: v1
kind: Service
metadata:
name: myapp-service
spec:
type: NodePort
ports:
- targetPort: 80
    port: 80
    nodePort: 30008
selector:
    app: myapp
    type: front-end
```

To create the service
```
$ kubectl create -f service-definition.yaml
```

To list the services
```
$ kubectl get services
```

To access the application from CLI instead of web browser
```bash
$ curl http://<ip-address>:<port>
```
    
#### 2. ClusterIP
- In this case the service creates a **`Virtual IP`** inside the cluster to enable communication between different services such as a set of frontend servers to a set of backend servers.

To create a service of type ClusterIP
```
apiVersion: v1
kind: Service
metadata:
 name: back-end
spec:
 types: ClusterIP
 ports:
 - targetPort: 80
   port: 80
 selector:
   app: myapp
   type: back-end
```
```
$ kubectl create -f service-definition.yaml
```

To list the services
```
$ kubectl get services
```

#### 3. LoadBalancer
- Where the service provisions a **`loadbalancer`** for our application in supported cloud providers. With a simple YAML file declaring your service name, port, and label selector, the cloud controller will provision a load balancer for you automatically.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: search-svc # the name of our service
spec:
  type: loadBalancer
  selector:
    app: SearchApp # pods are deployed with the label app=SearchApp
  ports:
    - port: 80
```
This is great because of how simple it is to put a load balancer in front of your application. The service spec has been extended over the years with annotations and additional configuration. A second option is to use an ingress rule and an ingress controller to route external traffic into Kubernetes pods.