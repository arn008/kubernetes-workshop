# Kubernetes workshop

## Welkom!

#### 1. Installeer kubectl
Om ons Kubernetes cluster te managen, moet kubectl (de CLI client) geinstalleerd zijn.
Kies de manier die bij jouw machine past: [https://kubernetes.io/docs/tasks/tools/install-kubectl/](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

#### 2. Configureer kubectl
Nu moeten we kubectl nog configureren om het workshop cluster te gebruiken:

```
kubectl config set-cluster kubernetes-workshop --server=<server> --insecure-skip-tls-verify=true
kubectl config set-context kubernetes-workshop-context --cluster=kubernetes-workshop --user=<username> --namespace=<username>
kubectl config set-credentials <username> --token=<token>
kubectl config use-context kubernetes-workshop-context
```

De variabelen hiervoor ontvang je apart.

#### 3. Starten maar!
1. Begin met de [basis](basics.md) opdrachten om kubernetes te leren kennen.
2. Ga vervolgens door met de [advanced](advanced.md) opdrachten.

Voer de opdrachten uit op eigen tempo. Je hoeft ze niet allemaal te voltooien.
