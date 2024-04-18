# TP DEPLOYER UN POD SPRING BOOT SUR KUBERNETES

## Pre-requis
```
 - Java 21
 - Docker
 - Minikube
 - Kubectl
 - Extension Yaml Redhat Vscode
```

## 1 - cloner le projet se trouvant à l'adresse suivante
```
 https://github.com/andresetevejob/kubernetes-app
```
## 2 - construire le projet
```
- mvn clean install
```

## 3 - Créer au fichier Dockerfile au sein du projet spring boot et y ajouter ce contenu
```
FROM eclipse-temurin:21
WORKDIR /opt
COPY target/kubernetes-app-0.0.1-SNAPSHOT.jar /app.jar
ENV PORT 8080
EXPOSE 8080
ENTRYPOINT exec java $JAVA_OPTS -jar app.jar
```

## 4 - Construire l'image docker 
```
- docker build -t knextapp:1.0.0 .
```

## 5 - Démarrer l'image avec Docker
```
docker run \
  --name=knextapp:1.0.0 \
  --rm \
  -p 8080:8080 \
  knextapp
```

## 6 - Faire un appel à notre application via postman
> Copier la commande ci-dessous dans le champ de saisi d'adresse de votre postman
```
 curl --location 'http://localhost:8080/info'
```


## 7 - Démarrer minikube
```
minikube start
```
## 8 - Faire un appel au cluster minikube pour s'assurer qu'il répond
```
 kubectl cluster-info
```
## 9 - Créer notre fichier de description de pod appeler le knextapp.yml et ajoutez-y le contenu ci-dessous:
```
apiVersion: v1
kind: Pod
metadata:
  name: knextapp
spec:
  containers:
    - image: knextapp:1.0.0
      name: knextapp
      ports:
        - containerPort: 8080
      protocol: TCP

```
## 10 - chargement de l'image dans le registry de minikube et creation du pod sur kubernetes via kubectl
> IL faudrait savoir que minikube possède son propre registre qui est différent de celui de notre environnement docker installé.Nous devons donc charger notre image docker dans le cluster minikube.IL existe d'autres façon de faire notamment en poussant notre image sur le registry docker public
```
 kubectl apply -f knextapp.yml
```

## 11 - Voir la ressource crée
```
 kubectl get pods -o wide
```
> l'option -o wide permet d'afficher plus de détails sur les pods 
## 12 - Accès à un pod
*  Le pod que nous venons de créer n'est accessible que depuis minikube (le cluster), pour le rendre accéssible de l'extérieur, nous allons voir une 
 première façon de faire.
```
kubectl port-forward knextapp 8081:8080

```
## 13 - Voir les logs au sein du pod
```
kubectl logs knextapp -f
```
> l'option -f permet de voir les logs en continue

## 14 - Se connecter au pod depuis la ligne de commande
```
kubectl exec -it knextapp -- /bin/bash
```
## 15 - Assigner un label à un pod
* cloner le fichier knextapp.yml sous un nom knextapp-with-labels, et ajouter le contenu suivant en dessous du noeud metadata
```
labels:
    creation_method: manual
    env: prod
```
## 16 - Créer le nouveau pod avec label
```
kubectl apply -f knextapp-with-label
```
## 17 - Lister les pods avec les labels
```
kubectl get po -L creation_method,env
```
## 18 - Afficher uniquement les pods qui ont le label creation_method valorisé à manual
```
kubectl get po -l creation_method=manual
```
## 19 - Modifier le label d'un pod existant
```
kubectl label po knextapp creation_method=manual
```
## 20 - Mettre à jour une valeur de label existante
```
kubectl label po knextapp-with-label env=debug --overwrite
```

## 21 - Afficher les informations sur un pod
```
kubectl describe po knextapp
```

## 21 - Suppression de pod
```
kubectl delete po knextapp
```

## 22 - Administrer son cluster depuis le dashboard de minikube
```
minikube dashboard
```

## Bilan du TP
* Savoir créer une image docker pour son application
* Deployer son image dans un pod kubernetes
* Interagir avec le pod
* Assigner un label à pod
* Accéder à l'application au sein du pod depuis l'exterieur (en dehors du cluster)


## A faire
* Créer une image docker pour une application hello-world en react
* Déployer cette image dans un pod kubernetes
* Accéder à cette application depuis l'exterieur (en dehors du cluster) depuis votre navigateur
* Deployer une instance v2 de l'application en lui assignant le label env valoriser à prod
* Modifier le label de l'instance v1 en lui assignant le label dev
* Modifier le title de l'application et recharger la page web
