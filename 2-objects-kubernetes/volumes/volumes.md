# KUBERNETES - VOLUMES

## 1 - Définition
* En cas de destruction d'un pod, tous les fichiers stockées sur ce dernier sont perdus.Comment permettre au nouveau pod d'avoir accès aux fichiers du précédent.
* Pour ce faire Kubernetes met en place le principe de volume qui permet de stocker les données d'un pod et les récuperer, même après desctruction.Attention tous les types de volumes de kubernetes, ne le permettent pas.

## 2 - Les différents types de volumes

* Kubernetes proposent différents types de volumes dans ce cours nous n'enverrons que trois types

### 2.1 - emptyDir
* ce type de volume permet un partage des informations entre les containers d'un pod.Leur durée de vie est liée à celle du pod lorsque le pod est détruit on perd les données stockées dans ce volumes

Exemple : 
* Ecrivez ce contenu dans un fichier emptyDir.yml
```
apiVersion: v1
kind: Pod
metadata:
  name: myvolumes-pod
spec:
  containers:
  - image: alpine
    imagePullPolicy: IfNotPresent
    name: myvolumes-container-1
    
    command: ['sh', '-c', 'echo The Bench Container 1 is Running ; sleep 3600']
    
    volumeMounts:
    - mountPath: /demo1
      name: demo-volume

  - image: alpine
    imagePullPolicy: IfNotPresent
    name: myvolumes-container-2
    
    command: ['sh', '-c', 'echo The Bench Container 2 is Running ; sleep 3600']
    
    volumeMounts:
    - mountPath: /demo2
      name: demo-volume

  - image: alpine
    imagePullPolicy: IfNotPresent
    name: myvolumes-container-3
    
    command: ['sh', '-c', 'echo The Bench Container 3 is Running ; sleep 3600']
    
    volumeMounts:
    - mountPath: /demo3
      name: demo-volume

  volumes:
  - name: demo-volume
    emptyDir: {}
```
* executer la commande kubectl apply -f emptydir.yml

* connectez vous sur un conteneur du pod et executez les commandes ci dessous : 
  ```
   . kubectl exec myvolumes-pod -c myvolumes-container-1 -i -t -- /bin/sh
   . ls
   . echo test1 > demo1/textfile1
  ```
 * ouvrez un autre terminal et connecter vous sur le second conteneur du pod et executez les commandes ci-dessous: 
 ```
   . kubectl exec myvolumes-pod -c myvolumes-container-2 -i -t -- /bin/sh
   . ls
   . ls demo2/
 ```


* IL peut être type de memory, c'est à dire que la donnée est stockée en memoire et non sur le filesystem
```
volumes:
- name: html
  emptyDir:
  medium: Memory
```

2.2 hostPath
* Ce type de volumes est stocké sur le pod qui heberge le pod.Quand le pod est supprimé les fichiers sont maintenus et peuvent être lu par un pod qui pointe sur le même volume
L'inconvenient du hostpath, si le pod après destruction est crée sur un autre noeud, il ne pourra plus voir les données du volumes

