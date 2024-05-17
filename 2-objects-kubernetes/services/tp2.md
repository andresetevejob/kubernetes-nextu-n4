# TP 2 SERVICES KUBERNETES

### 1 - Contexte
* Déploiement dans kubernetes de notre application fullstack
* Les composants concernés : java et mongodb
* Exigence : ingress

## 2 -  Pointer son docker client vers l'instance docker de minikube
* eval $(minikube docker-env -p nextu) 

## 3  - build

* mvn clean install (vous pouvez utilisez le plugin maven d'intellij)
* docker build . -t knextapp:2.0.0

* NB :  vérifier que vous êtes bien en java 21 avant le build maven

## 4 -  créer un nouveau namespace
* kubectl apply -f k8s/knextapp-namespace-minio.yml

## 5 - Deploiement mongodb

* créer un fichier mongo.yml et y rajouter le contenu ci-dessous :
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mongo-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 256Mi
---
apiVersion: v1
kind: Service
metadata:
  name: mongo
spec:
  selector:
    app: mongo
  ports:
    - port: 27017
      targetPort: 27017
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongo
spec:
  selector:
    matchLabels:
      app: mongo
  template:
    metadata:
      labels:
        app: mongo
    spec:
      containers:
        - name: mongo
          image: mongo
          ports:
            - containerPort: 27017
          volumeMounts:
            - name: storage
              mountPath: /data/db
      volumes:
        - name: storage
          persistentVolumeClaim:
            claimName: mongo-pvc
```
* kubectl apply -f mongo.yml

## 6 - créer un pod minio
* creer un fichier minio.yml et y rajouter le contenu ci-dessous :
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: minio-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 256Mi
---
apiVersion: v1
kind: Service
metadata:
  name: minio
spec:
  selector:
    app: minio
  ports:
    - port: 9000
      targetPort: 9000
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: minio
spec:
  strategy:
    type: Recreate
  selector:
    matchLabels:
      app: minio
  template:
    metadata:
      labels:
        app: minio
    spec:
      containers:
        - name: minio
          image: minio/minio:latest
          args:
            - server
            - /storage
          env:
            - name: MINIO_ACCESS_KEY
              value: mykey
            - name: MINIO_SECRET_KEY
              value: mysecret
          ports:
            - containerPort: 9000
          volumeMounts:
            - name: storage
              mountPath: /storage
      volumes:
        - name: storage
          persistentVolumeClaim:
            claimName: minio-pvc
```
* kubectl apply -f k8s/minio.yml -n knextminio

## 7 - déployez l'application
* kubectl apply -f k8s/knextapp-deploy.yml -n knextminio

## 8 - deployer le service nodeport
* kubectl apply -f k8s/knextapp-svc-lb-port.yml -n knextminio

## 9 - activer l'adresse externe ip du service avec minikube

* minikube service knextapp-svc-lb-port