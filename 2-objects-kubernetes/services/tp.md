# TP SERVICES KUBERNETES

### 1 - Mise en place d'un cluster IP
créer un fichier yml : knextapp-svc.yml et rajouter le contenu ci-dessous : 
```
apiVersion: v1
kind: Service
metadata:
  name: myknappsvc
spec:
  ports:
    - port: 80
      targetPort: 8080
  selector:
    app: myknappod

```
Kubernetes lie un pod à un service à partir du label app

* Voir les variables d'environnement sur son pod : kubectl exec pod_name env
* 

### 2 - Mise en place d'un NodePort
créer un fichier yml : knextapp-svc-node-port.yml et rajouter le contenu ci-dessous : 

```
apiVersion: v1
kind: Service
metadata:
    name: myknappsvc-nodeport
spec:
    type: NodePort
    ports:
    - port: 80
      targetPort: 8080
      nodePort: 30123
    selector:
      app: myknappod
```
accéder au service depuis l'exterieur on peut faire l'une des options suivantes
* minikube ip : pour récuperer l'adresse ip
* minikuber service nom_service ; cela ouvre l'application dans le navigateur
* minikube service nom_service --url :  cela donne l'url du service
### 3 - Mise en place d'un LoadBalancer


```
apiVersion: v1
kind: Service
metadata:
    name: myknappsvc-loadbalancer
spec:
    type: LoadBalancer
    ports:
    - port: 80
      targetPort: 8080
    selector:
      app: myknappod
```

### 4  - Mise en place d'ingress

```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: knextapp-ingress
spec:
  rules:
  - host: nextapp.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: myknappsvc-nodeport
            port:
              number: 8080
```
* NB :  sur une machine local, il faut modifier le /etc/hosts


* Ingress peut gérer plusieurs domaines : 
```
spec:
rules:
    - host: foo.example.com
      http:
        paths:
        - path: /
          backend:
          serviceName: foo
          servicePort: 80
    - host: bar.example.com
      http:
        paths:
        - path: /
          backend:
          serviceName: bar
          servicePort: 80
```