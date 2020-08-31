# Basics
The principles of Kubernetes.

*Whenever you need to choose an port for an pod or service: all applications are running on port 8080!*

## Goals
1. Start an pod on our cluster
2. Expose pod through a service
3. Convert Pod to deployment
4. Add Readiness and liveness probes
5. Testing Resilience of deployment
6. Inter Pods communication

### 1. Start an Pod
We start with a single pod. A single pod isn't used often. Later on we will see why :). But this will supply a good start towards a deployment.

We will start with deploying a playground application.

Create a .yaml file with a pod definition:
 - 1 container: `rubenernst/kubernetes-playground:1.0.2`.
 - label: `app=playground`
 - specify port 8080

Start the pod

More information: https://kubernetes.io/docs/concepts/workloads/pods/
Example of pod resource:
```
apiVersion: v1
kind: Pod
metadata:
  name: cpu-demo
  namespace: cpu-example
spec:
  containers:
  - name: cpu-demo-ctr
    image: vish/stress
```
 
### 2. Expose pod
Now that your pod is active in the cluster, we will make it accessible to the internet. This will be done with a service.

Create a .yaml file with an service definition:
 - type: `loadbalancer`
 - configure the ports (8080)
 
Visit this service and try to reach the pod: `http://<ip>:<poort>/actuator/info`

Hints:
 - Think about the label (`app=playground`)
 - IP through `kubectl get service`
 
 More information: https://kubernetes.io/docs/concepts/services-networking/service/
 
### 3. Convert Pod to Deployment
A single Pod isn't used often. It's not managed properly: when a node crashes no new pods are created. That's why they introduced ReplicationControllers. Those ReplicationControllers are being managed by deployment because of rolling updates.
 
Create a .yaml or .json file with a deployment for our playground application. Set the replicas to three. Add the selector `app=openvalue`. Don't remove the service from previous step, the selector will add new pods to the service.

Check whether load balancing is working by visiting the app at `/actuator/info`. The hostname will change after some refreshes. 

Hints:
 - First remove the old pod (`kubectl delete -f <file>` or `kubectl delete pod <name>`)
 
 More information: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
 
### 4. Readiness and liveness probes
Kubernetes has excellent support for healthchecks. Whenever a pod gets unhealthy (for an certain period), it will be restarted automatically.
Our playground application has an build-in healthcheck: `actuator/health`.

Configure this endpoint as readyness and liveness probes. The application will start in approximately 10-15 seconds. Take this in consideration when configuring the parameters. 

Check at start-up what is changing in the `READY` column for `kubectl get pods`.

Hints:
 - When `CrashLoopBackOff` occurs the parameters are too tight :).
 - `kubectl get pods --watch` to see changes to pods instant
 - `kubectl describe pod <id>` to check events of the pod
 
 More information: https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/
 
### 5. Resilience testing of deployment
It's time to test our liveness probes :).

Call the endpoint `/actuator/unhealthy` on 1 of the pods through the service.
Check what happens with the pods within kubernetes (`kubectl get pods --watch`)

Our pods are part of a deployment with a fixed amount of replicas. Delete a pod manually and take a look what kubernetes will do with the pods.
  
### 6. Communication between pods
Until now all pods are being used standalone, but most of the time communication to other services are need: a database, other service, eventbus etc.

We will do the same! There are two docker images available: `rubenernst/kubernetes-user-service:1.0.0` and `rubenernst/kubernetes-user-service-proxy:1.0.0`. As the name of the pod indicates, the last container is a proxy for the first container.

Setup two deployments. The last container (proxy) needs to forward HTTP requests to the first container (user-service). The usual way to set this up, is by adding evironment variables. The environment variable for the proxy are `USER_SERVICE_HOST` and `USER_SERVICE_PORT`. 

Test if the proxy works by calling it through the browser.

Hints:
 - Services (also internal services) :)
 - containerPort needs to be the port exposed by the container
 - Services will get an local DNS: `<service>`.`<namespace>`.svc.cluster.local
