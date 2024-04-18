# KUBERNETES NAMESPACE

# I - Namespace
* Les namespaces sont des objets kubernetes utiliser pour regrouper des ressources
* Exemple : créer un fichier custom-namespace.yml et ajoutez le contenu ci-dessous
```
apiVersion: v1
kind: Namespace
metadata:
  name: knextnamespace
```
* faire la commande kubectl apply -f custom-namespace
* Assigner un pod à un namespace à la creation
```
kubectl apply -f knextapp.yml -n knextnamespace
```
* voir les différents namespace au sein de votre application
```
kubectl get ns
```

* voir les pods de notre namespace
```
kubectl get po --namespace knextappnamespace
```
