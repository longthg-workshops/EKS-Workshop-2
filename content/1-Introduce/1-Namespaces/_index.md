---
title: "Namespaces"
weight: 1
chapter: false
pre: "<b> 1.1 </b>"
---

### Namespace introduction

In this section, we will learn about **Namespaces**.

In the EKS workshops, we would have to create objects like **Pods**, **Deployments** and **Services** in our cluster. All said objects, was in the same **namespace**. 

The default **Namespace** in Kubernetes is the one automatically created when Kubernetes is initialized and configured.

### Command

You can use the following command to list all namespaces in your cluster:

```bash
kubectl get namespaces
```

You can create your own namespace:

```bash
kubectl create namespace dev
```

To list all pods in the default namespace:

```bash
kubectl get pods
```

To list all pods in a non-default namespace:

```bash
kubectl get pods --namespace=kube-system
```
_* Replace kube-system with a namespace you wish to check_

When creating a pod with the manifest file, without the **`namespace`** field, the pod will be created in the default namespace:


```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
     app: myapp
     type: front-end
spec:
  containers:
  - name: nginx-container
    image: nginx
```

```bash
kubectl create -f pod-definition.yaml
```

To create a **Pod** with the same **Pod** definition file in a different **namespace**, use the `--namespace` option.

```bash
kubectl create -f pod-definition.yaml --namespace=dev
```

If you want to ensure that this **Pod** is always created in the **dev** environment, even when not specified in the command line, you can move the `--namespace` definition into the **Pod** definition file.

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: dev
```

```bash
kubectl create -f namespace-dev.yaml
```

Or use the shorter command:

```bash
kubectl create namespace dev
```

By default, we will be in a default **namespace**. To switch to a specific **namespace** permanently, run the following command:

```bash
kubectl config set-context $(kubectl config current-context) --namespace=dev
```

To list all Pods in all namespaces:

```bash
kubectl get pods --all-namespaces
```

To limit resources within a **namespace**, create a **resource quota**. To create one, start with the **ResourceQuota** definition file.

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-quota
  namespace: dev
spec:
  hard:
    pods: "10"
    requests.cpu: "4"
    requests.memory: 5Gi
    limits.cpu: "10"
    limits.memory: 10Gi
```

```bash
kubectl create -f compute-quota.yaml
```

### References

- [Kubernetes - Overview - Working with Objects - Namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)
- [Kubernetes - Tasks - Administer Cluster - Namespaces Walkthrough](https://kubernetes.io/docs/tasks/administer-cluster/namespaces-walkthrough/)
- [Kubernetes - Tasks - Administer Cluster - Namespaces](https://kubernetes.io/docs/tasks/administer-cluster/namespaces/)
- [Kubernetes - Tasks - Administer Cluster - Manage Resources - Quota Memory CPU - Namespace](https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/quota-memory-cpu-namespace/)
- [Kubernetes - Tasks - Access Application Cluster - List All Running Container Images](https://kubernetes.io/docs/tasks/access-application-cluster/list-all-running-container-images/)
