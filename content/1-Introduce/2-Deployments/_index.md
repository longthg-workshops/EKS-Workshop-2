---
title: "Deployment trong Kubernetes"
weight: 2
chapter: false
pre: "<b> 1.2 </b>"
---

In this section, we will take a look at Kubernetes **Deployment**s

### Introduction to Deployments
#### What is a Deployment ?
A Deployment provides declarative updates for Pods and ReplicaSets.

You describe a desired state in a Deployment, and the Deployment Controller changes the actual state to the desired state at a controlled rate. You can define Deployments to create new ReplicaSets, or to remove existing Deployments and adopt all their resources with new Deployments.

{{% notice note %}}
Do not manage ReplicaSets owned by a Deployment. Consider opening an issue in the [main Kubernetes GitHub repository](https://github.com/kubernetes/kubernetes/issues) if your use case is not covered below.
{{% /notice %}}

#### Use cases
The following are some typical use cases for Deployments:

- Create a Deployment to rollout a ReplicaSet. The ReplicaSet creates Pods in the background. Check the status of the rollout to see if it succeeds or not.

- Declare the new state of the Pods by updating the PodTemplateSpec of the Deployment. A new ReplicaSet is created and the Deployment manages moving the Pods from the old ReplicaSet to the new one at a controlled rate. Each new ReplicaSet updates the revision of the Deployment.

- Rollback to an earlier Deployment revision if the current state of the Deployment is not stable. Each rollback updates the revision of the Deployment.

- Scale up the Deployment to facilitate more load.

- Pause the rollout of a Deployment to apply multiple fixes to its PodTemplateSpec and then resume it to start a new rollout.

- Use the status of the Deployment as an indicator that a rollout has stuck.

- Clean up older ReplicaSets that you don't need anymore.

### Creating a Deployment
#### Deployment definition file

**`deployment-definition.yaml`**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

In this example:

- A Deployment named `nginx-deployment` is created, indicated by the `.metadata.name` field. This name will become the basis for the ReplicaSets and Pods which are created later. See Writing a Deployment Spec for more details.

- The Deployment creates a ReplicaSet that creates three replicated Pods, indicated by the `.spec.replicas` field.

- The `.spec.selector` field defines how the created ReplicaSet finds which Pods to manage. In this case, you select a label that is defined in the Pod template (app: nginx). However, more sophisticated selection rules are possible, as long as the Pod template itself satisfies the rule.

{{% notice note %}}
The `.spec.selector.matchLabels` field is a map of {key,value} pairs. A single {key,value} in the matchLabels map is equivalent to an element of matchExpressions, whose key field is "key", the operator is "In", and the values array contains only "value". All of the requirements, from both matchLabels and matchExpressions, must be satisfied in order to match.
{{% /notice %}}

- The template field contains the following sub-fields:

    - The Pods are labeled `app: nginx` using the `.metadata.labels` field.

    - The Pod template's specification, or `.template.spec` field, indicates that the Pods run one container, `nginx`, which runs the **_nginx_** Docker Hub image at version 1.14.2.

    - Create one container and name it nginx using the `.spec.template.spec.containers[0].name` field.

#### Commands

After the manifest file is ready, you can run the `kubectl create` command with the said file as the input:

```bash
$ kubectl create -f deployment-definition.yaml
```

To see the Deployment you have just created, use the following command:

```bash
$ kubectl get deployment
```

The Deployment will automatically create a ReplicaSet. You can see that using the following command:

```bash
$ kubectl get pods
```

To see all objects at once:

```bash
$ kubectl get all
```

### References
- [Deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)
- [Deploying an app](https://kubernetes.io/docs/tutorials/kubernetes-basics/deploy-app/deploy-intro/)
- [Manage your Deployments](https://kubernetes.io/docs/concepts/cluster-administration/manage-deployment/)
- [Working with Kubernetes Objects](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/)