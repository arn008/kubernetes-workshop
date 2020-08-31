# Kubernetes workshop

## Welcome!

#### 1. Install kubectl
To manage our Kubernetes, kubectl (the CLI client) needs to be installed.
Choose the correct way for your machine: [https://kubernetes.io/docs/tasks/tools/install-kubectl/](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

#### 2. Configure kubectl
Now we have to configure kubectl to use our workshop cluster:

```
kubectl config set-cluster kubernetes-workshop --server=<server> --insecure-skip-tls-verify=true
kubectl config set-context kubernetes-workshop-context --cluster=kubernetes-workshop --user=<username> --namespace=<username>
kubectl config set-credentials <username> --token=<token>
kubectl config use-context kubernetes-workshop-context
```

The variables you will recieve separately.

#### 3. Go ahead!
1. Start with [basics](basics.en.md) exercises to learn more about kubernetes
2. Continue with the [advanced](advanced.en.md) exercises

Execute the exercises on your own pace. You don't have to complete all exercises.
