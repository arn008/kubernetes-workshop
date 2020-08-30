
# Advanced
Geadvanceerdere topic van Kubernetes

## Doelen
1. Autoscale geactiveerd
2. Ingress
3. Secrets & ConfigMaps

### 1. Autoscale
Deploy een pod (via een deployment) met de playground image (`rubenernst/kubernetes-playground:1.0.2`). 1 replica. Maak ook weer een service aan (hoeft niet extern te zijn).

Maak ook een autoscale aan. Dit kan via een HorizontalPodAutoscaler yaml definitie, maar het mag ook makkelijker: 

`kubectl autoscale deployment <deployment name> --cpu-percent=50 --min=1 --max=10`

In productie wil je natuurlijk wel een yaml definitie gebruiken.

Voer vervolgens dit uit:

`kubectl run -i --rm --tty load-generator --image=busybox /bin/sh`

en dan:

`while true; do wget -q -O- http://<service ip>:<port>/calculate; done`

Laat dit een tijdje draaien.

Bekijk de load met `kubectl get hpa` en hoeveel pods opgestart worden (`kubectl get pods`). Kan even duren (ook weer bij het afschalen). Is configureerbaar.

Voor meer informatie en uitgebreidere configuratie, check: [https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/).

### 2. Ingress
We willen graag een 'API gateway' voor onze applicatie opzetten in plaats van een nieuw extern IP adres voor iedere load balancer service. Kubernetes kent hiervoor Ingress.

Maak een deployment en service (geen load balancer) voor zowel `rubenernst/kubernetes-playground:1.0.2` als `rubenernst/kubernetes-user-service:1.0.1`.

Maak daarna een Ingress die `/playground` door route naar de playground applicatie en `/users` door route naar de user service. Het kan een paar minuten duren voor de ingress opgezet is. IP krijg je via `kubectl get ingress`. Zelfs na het IP kan het zijn dat er 404's komen. Nog even geduld hebben :). 

Hints:
 - Specifieer geen host.
 - De twee services voor de applicaties moeten van het type NodePort zijn.
 
 ### 3. Secrets
 Secrets zijn er om veilig wachtwoorden op te slaan. Voor bijvoorbeeld database authenticatie. 
 Voor de playground applicatie gaan we dat simuleren door de version te zetten via een secret.
 
 Maak een secret aan met een versienummer en koppel deze aan environment variabele `VERSION` van de playground image (`rubenernst/kubernetes-playground:1.0.2`). Maak er een deployment van die via een load balancer te benaderen is. 
 
 Check de versie door endpoint `/actuator/info` aan te roepen.
 
 ### 4. ConfigMaps
 Probeer hetzelfde als opdracht #3 te doen, maar dan via een ConfigMap. Want properties die geen gevoelige data bevatten, kunnen beter via een ConfigMap beheerd worden.
 
