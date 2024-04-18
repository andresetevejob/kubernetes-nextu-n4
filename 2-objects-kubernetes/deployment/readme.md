# DEPLOYMENT
* Objet kubernetes permettant de lanncer la création de un ou plusieurs pods
## 1 -  Créer un deploiement
* créer un fichier 

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myknappdeploy
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myknappod
  template:
    metadata:
      labels:
        app: myknappod
    spec:
      containers:
      - name: myknextapp
        image: knextapp:1.0.0
        ports:
        - containerPort: 8080
```        
## 2 - voir l'historique du déploiement
```
kubectl rollout history deployment myknappdeploy
```
## 3 - Modifier la cause d'un changement 
```
kubectl annotate deployment/myknappdeploy kubernetes.io/change-cause="update image version from 1.0.0"
```
## 4 - Modifier le fichier de déploiement en change la version de l'image
> IL faut avoir créer une nouvelle version de l'image
* passer par exemple de la version 1.0.0 à la version 1.0.1

## 5 - Modifier la cause du changement 
kubectl annotate deployment/myknappdeploy kubernetes.io/change-cause="update image version from 1.0.0 to 1.0.1"
## 6 Modifier l'image via kubectl ainsi que la cause du changement
> IL faudrait avoir une version 1.0.2 de notre image
```
- kubectl set image  deployment myknappdeploy mycontainer=knextapp:1.0.2
- kubectl annotate deployment/myknappdeploy kubernetes.io/change-cause="update image version from 1.0.1 to 1.0.2"
deployment.apps/mydeployment annotated
```

## 7 - réafficher l'historique de déploiement
```
kubectl rollout history deployment myknappdeploy
```

## 8 - revenir sur une révision antérieur
```
kubectl rollout undo --to-revision=2 deployment/myknappdeploy
```