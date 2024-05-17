# FIX INGRESS
* Pre-réquis : IL faudrait vous positionner dans le dossier kubernetes-app avant l'execution des commandes, et sur la branche kubernetes-fix-ingress

## 1 -  créer un nouveau profile minikube
* minikube start -p nextu

## 2 -  Pointer son docker client vers l'instance docker de minikube
* eval $(minikube docker-env -p nextu) 

## 3  - build

* mvn clean install (vous pouvez utilisez le plugin maven d'intellij)
* docker build . -t knextapp:1.0.0

* NB :  vérifier que vous êtes bien en java 21 avant le build maven

## 4 -  créer un nouveau namespace
* kubectl apply -f k8s/knextapp-namespace-fix-ingress.yml

## 5 - déployez l'application
* kubectl apply -f k8s/knextapp-deploy.yml -n knextnamespace

## 6 - deployer le service nodeport
* kubectl apply -f k8s/knextapp-svc-node-port.yml -n knextnamespace

## 7 - activer ingress dans le namespace
* minikube addons enable ingress -p nextu

## 8 - deployer ingress
* kubectl apply -f k8s/knextapp-ingress.yml -n knextnamespace

## 9 - modifier le /etc/hosts
* sudo vi /etc/hosts
* rajouter la ligne suivante : 
minikube_ip nextapp.com

## 10 - Résultat
* tapez cette url dans le navigateur:  http://nextapp.com/