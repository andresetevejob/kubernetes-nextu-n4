# KUBERNETES - SERVICES

## 1 - Definition
 *  Un service est tout d'abord un objet kubernetes (tout comme les pods ou ConfigMap). Qui sert comme un seul point d'entrée pour un groupe de pod.
 Les services sont liées au pod au moyen des selectors

## 2 - Les différents type de services 

#### 2.1 -  Cluster IP
* IL permet une communication avec ou entre les composants au sein du cluster.Kubernetes assigne une adresse ip virtuel au sein du cluster au service.
* Exemple: nous avons une application java et une base de mongodb qui se trouve sur le même cluster.Le service du pod mongodb peut être uniquement qu'accessible en mode cluster IP


#### 2.2 -  NodePort
* Les NodePort permettent une communication depuis l'externe avec les pods mais en ouvrant le même port sur les différents noeuds kubernetes.

#### 2.3 - LoadBalancer

* ILs permettent une communication non plus via un port, mais une adresse ip unique, le loadbalancer est une couche au dessus du nodeport.

#### 2.4 -  Ingress

* Ingress est un objet Kubernetes, qui permet d'accéder aux services depuis l'extérieur.
* Avec Ingress, nous avons un seul point d'entrée pour les différents services qu'il couvre.
* Ingress met en place des règles de routage, définit le protocol