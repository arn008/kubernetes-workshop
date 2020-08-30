
# Advanced
Advanced topic for Kubernetes

## Goals
1. Activate Autoscale
2. Ingress
3. Secrets & ConfigMaps

### 1. Autoscale
Deploy an pod(through an deployment) with the playground image (`rubenernst/kubernetes-playground:1.0.2`). 1 replica. Also create a service (doesn't have to be an external).

Create an autoscale. This can be done through a HorizontalPodAutoscaler yaml definition, but can be done more easily by:

`kubectl autoscale deployment <deployment name> --cpu-percent=50 --min=1 --max=10`

In production you want to user the yaml definition.

Execute:
`kubectl run -i --rm --tty load-generator --image=busybox /bin/sh`

After that:

`while true; do wget -q -O- http://<service ip>:<port>/calculate; done`

Let this run for an bit.

Look at the load through `kubectl get hpa` and how many pods are started(`kubectl get pods`). It can take an while before it upscales (the same for downscaling). This can be configured.

More information and extended configuration: [https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/).

### 2. Ingress
We would like to setup an 'API gateway' for our application in stead of an new external IP for every load balancer service. Kubernetes introduced the Ingress for this.

Create an deployment and service (no load balancer) for both `rubenernst/kubernetes-playground:1.0.2` and `rubenernst/kubernetes-user-service:1.0.1`.

Create an ingress that will route `/playground` to the playground application and `/users` to the user service. It can take up to a couple of minutes until the ingress is setup. You can receive the IP through `kubectl get ingress`. Even after the IP is supplied you can get 404's. Please have more patients :).

Hints:
 - Specify no host.
 - The two service need to be of Type NodePort.
 
 ### 3. Secrets
 Secrets are the way in kubernetes to save passwords safely. This can be used for instance database authentication.
 For the playground application we will simulate this by setting the version through a secret.
 
 Create a secret with an versionnumber and couple this secret to the environment variable `VERSION` for the playground image (`rubenernst/kubernetes-playground:1.0.2`). Create a load balancer to connect to this deployment. 
 
 Check the version by calling the endpoint `/actuator/info`.
 
 ### 4. ConfigMaps
 Try doing the same thing as in excersise #3, but now use ConfigMap. ConfigMap can be used for non-sensitive dat and can be better managed.
