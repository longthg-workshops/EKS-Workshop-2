---
title: "ReplicaSets"
weight: 1
chapter: false
pre: "<b> 1.1 </b>"
---

In this section, we will take a look at these concepts
- Replication Controller
- ReplicaSet

### Introduction
#### What are Replicas ?
Kubernetes **Replicas** are clones that facilitate self-healing for pods. As with most processes and services, pods are liable to failure, errors, evictions, and deletion. For instance, pods may fail and be subsequently evicted when there is a sudden drop in system resources and an increase in node pressure.

#### ReplicationController
A ReplicationController ensures that a specified number of pod replicas are running at any one time. If there are too many pods, the ReplicationController terminates the extra pods. If there are too few, the ReplicationController starts more pods. Unlike manually created pods, the pods maintained by a ReplicationController are automatically replaced if they fail, are deleted, or are terminated. For example, your pods are re-created on a node after disruptive maintenance such as a kernel upgrade. For this reason, you should use a ReplicationController even if your application requires only a single pod.

For short, a ReplicationController makes sure that a pod or a homogeneous set of pods is always up and available. A ReplicationController is similar to a process supervisor, but instead of supervising individual processes on a single node, the ReplicationController supervises multiple pods across multiple nodes.

ReplicationController is often abbreviated to "rc", and as a shortcut in `kubectl` commands.

#### ReplicaSet
Similar to a ReplicationController, a ReplicaSet's purpose is to maintain a stable set of replica Pods running at any given time. As such, it is often used to guarantee the availability of a specified number of identical Pods.

A ReplicaSet is defined with fields, including a selector that specifies how to identify Pods it can acquire, a number of replicas indicating how many Pods it should be maintaining, and a pod template specifying the data of new Pods it should create to meet the number of replicas criteria. A ReplicaSet then fulfills its purpose by creating and deleting Pods as needed to reach the desired number. When a ReplicaSet needs to create new Pods, it uses its Pod template.

A ReplicaSet is linked to its Pods via the Pods' metadata.ownerReferences field, which specifies what resource the current object is owned by. All Pods acquired by a ReplicaSet have their owning ReplicaSet's identifying information within their ownerReferences field. It's through this link that the ReplicaSet knows of the state of the Pods it is maintaining and plans accordingly.

A ReplicaSet identifies new Pods to acquire by using its selector. If there is a Pod that has no OwnerReference or the OwnerReference is not a Controller and it matches a ReplicaSet's selector, it will be immediately acquired by said ReplicaSet.

#### ReplicaSet vs. ReplicationController
ReplicaSets are the successors to ReplicationControllers. The two serve the same purpose, and behave similarly, except that a ReplicationController does not support set-based selector requirements as described in the labels user guide. As such, ReplicaSets are preferred over ReplicationControllers.

### Creating a Replication Controller
  - Replication Controller Definition File:
  
```yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: myapp-rc
  labels:
    app: myapp
    type: front-end
spec:
  template:
    metadata:
      name: myapp-pod
      labels:
        app: myapp
        type: front-end
    spec:
      containers:
      - name: nginx-container
        image: nginx
  replicas: 3
```
  - To Create the replication controller

```bash
kubectl create -f rc-definition.yaml
```

  - To list all the replication controllers

```bash
kubectl get replicationcontroller
```
  - To list pods that are launch by the replication controller

```bash
kubectl get pods
```
    
### Creating a ReplicaSet
  
  - ReplicaSet Definition File

```yaml
  apiVersion: apps/v1
  kind: ReplicaSet
  metadata:
    name: myapp-replicaset
    labels:
      app: myapp
      type: front-end
  spec:
    template:
      metadata:
        name: myapp-pod
        labels:
          app: myapp
          type: front-end
      spec:
        containers:
        - name: nginx-container
          image: nginx
    replicas: 3
    selector:
      matchLabels:
      type: front-end
```
**ReplicaSet requires a `selector` definition in comparison with Replication Controller.**
   
  - To Create the replicaset

```bash
kubectl create -f replicaset-definition.yaml
```
  - To list all the replicaset

```bash
kubectl get replicaset
```

  - To list pods that are launch by the replicaset

```bash
kubectl get pods
```
  
### How to scale replicaset
- There are multiple methods to scale replicaset:

  - **Method 1:** Update the number of replicas in the replicaset-definition.yaml definition file. E.g replicas: 6 

 ```yaml
    apiVersion: apps/v1
    kind: ReplicaSet
    metadata:
      name: myapp-replicaset
      labels:
        app: myapp
        type: front-end
    spec:
     template:
        metadata:
          name: myapp-pod
          labels:
            app: myapp
            type: front-end
        spec:
         containers:
         - name: nginx-container
           image: nginx
     replicas: 6
     selector:
       matchLabels:
        type: front-end
```
  
  Then run this command:

```bash
kubectl apply -f replicaset-definition.yaml
```
  - **Method 2:** Use the **`kubectl scale`** command.

```bash
kubectl scale --replicas=6 -f replicaset-definition.yaml
```

  You can also use **`kubectl scale`** command with type (e.g. `replicaset`) and name (e.g. `myapp-replicaset`) in place of the definition file:

```bash
kubectl scale --replicas=6 replicaset myapp-replicaset
```

### K8s Reference Docs:
- [Kubernetes: Replica](https://sematext.com/glossary/kubernetes-replica/)
- [Kubernetes: Replicaset](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/)
- [Kubernetes: Replication Controller](https://kubernetes.io/docs/concepts/workloads/controllers/replicationcontroller/)