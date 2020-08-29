# Basics
The principles of Kubernetes.

*Whenever you need to choose an port for an pod or service: all applications are running on port 8080!*

## Goals
1. Start an pod on our cluster
2. Expose pod through an service
3. Convert Pod to deployment
4. Add Readyness and liveness probes
5. Testing Resilience of deployment
6. Inter Pods communication

### 1. Start an Pod
We start with an single pod. An single pod isn't used often. Later on we will see why :). But this will supply an good start towards an deployment.

We will start with deploying an playground application.

Create an .yaml file with an pod definition:
 - 1 container: `rubenernst/kubernetes-playground:1.0.2`.
 - label: `app=playground`
 - specify port 8080

Start the pod
 
### 2. Expose pod
Now that your pod is active in the cluster, we will make it accessible to the internet. This will be done with an service.

Create an .yaml file with an service definition:
 - type: `loadbalancer`
 - configure the ports (8080)
 
Visit thie service and try to reach the pod: `http://<ip>:<poort>/actuator/info`

Hints:
 - Think about the label (`app=playground`)
 - IP through `kubectl get service`
 
### 3. Convert Pod to Deployment
An single Pod isn't used often. It's not managed properly: when an node crashes no new pods are created.That's why they introduced ReplicationControllers. Those ReplicationControllers are being managed by deployment because of rolling updates.
  
Definineer een .yaml of .json file met een deployment voor onze playground applicatie. Zet aantal replicas op drie. Voeg weer selector `app=openvalue` toe. Verwijder de service niet door de selector worden deze nieuwe pods automatisch aan de service gekoppeld.

Check of load balancing werkt door de app te bezoeken op `/actuator/info`. De hostname zou moeten wijzigen bij een aantal refreshes.

Hints:
 - Verwijder eerst de pod (`kubectl delete -f <file>` of `kubectl delete pod <name>`)
 
### 4. Readyness en liveness probes 
Kubernetes heeft uitstekende ondersteuning voor healthchecks. Als een pod unhealthy wordt (voor een bepaalde tijd), kan deze automatisch worden herstart.
Onze playground applicatie heeft ook een ingebouwde healthcheck: `/actuator/health`.

Configureer dit endpoint voor readyness en liveness probes. De applicatie start ongeveer in 10-15 seconden. Hou hier rekening mee bij het configureren van de parameters.

Check bij opstarten wat er veranderd aan de `READY` kolom bij `kubectl get pods`.

Hints:
 - Bij een `CrashLoopBackOff` staan de parameters te strak :).
 - `kubectl get pods --watch` om veranderingen aan pods direct te zien
 - `kubectl describe pod <id>` om events van een pod te zien
 
### 5. Resilience testen van deployment
Tijd om de liveness probes te testen :).

Roep endpoint `/actuator/unhealthy` aan op 1 van de pods via de service.
Check wat kubernetes doet met de pods (`kubectl get pods --watch`)

Onze pods zijn onderdeel van een deployment met een vast aantal replicas. Delete een pod eens handmatig en kijk wat kubernetes vervolgens doet met de pods.
  
### 6. Communicatie tussen pods
Tot nu toe hebben waren de pods standalone, maar vaak heb je communicatie naar iets anders nodig: een database, andere service, eventbus etc.

Dit gaan wij ook doen! Er zijn twee docker images beschikbaar: `rubenernst/kubernetes-user-service:1.0.0` en `rubenernst/kubernetes-user-service-proxy:1.0.0`. Zoals de naam aangeeft, is de laatste een proxy voor de eerste. 

Zet twee deployments op. De laatste (proxy) moet HTTP requests doorsturen naar de eerste (user-service). Een gebruikelijke manier om het endpoint van een service aan een andere service door te geven, zijn environment variabelen. De environment variabele voor de proxy zijn `USER_SERVICE_HOST` en `USER_SERVICE_PORT`. 

Test of de proxy werkt door deze in de browser aan te roepen.

Hints:
 - Services (ook interne services) :)
