 
# Sécurité des orchestrateurs

## Activités Pratiques

### 1. Déployer un Cluster Kubernetes avec Kind : 

On commence par créer un fichier `yaml` qui définit un **cluster** avec 2 master nodes et 2 worker nodes.

![Contenu du fichier Yalm qui définit un cluster](screen/Session3/Kind-cluster-fichier-yaml.png)

Dans un second temps on va venir créer notre **cluster Kubernetes** selon notre configuration avec la commande suivante : 

```bash
kind create cluster --config kind-cluster.yaml --name mon-cluster
```

Une fois le cluster déployé, on va vérifier son état : 

![Affichage dans le terminal de l'état de (mon-cluster)](screen/Session3/Etat-cluster.png)

Notre cluster a été créé avec succès, comme prévu, avec **2 master nodes** et **2 worker nodes**. Tous les nœuds sont à l'état `Ready`, ce qui signifie qu'ils sont prêts à exécuter des pods et à participer au fonctionnement du cluster.

Pour afficher les namespaces dans un cluster Kubernetes, il faut utiliser la commande suivante :  

```bash
kubectl get namespaces
```

Cette commande permet de lister tous les namespaces actifs dans le cluste, ainsi que leur statut et leur âge.

![Utilisation de la commande pour afficher les namespaces](screen/Session3/affichage-namespace.png)

Pour vérifier la version **kubernetes** déployé il faut utiliser la commande suivante : 

```bash
kubectl version
```

Dans notre cas on utilise la version client est **1.32.2**.
La version de Kubernetes qui tourne sur notre cluster est aussi **1.32.2** ce qui signifie que notre client et notre serveur sont bien compatibles.

![Affichage de la version kubernetes](screen/Session3/version-kubernetes.png)


### 2. Expérimentation des RBAC : 

On commence par créer un namespace `test-rbac` : 

```bash
kubectl create ns test-rbac
```

On crée un fichier `mon-pod.yalm` avec le contenu suivant : 

![Contenu du fichier mon-pod.yalm](screen/Session3/fichier-mon-pod-yaml.png)

On déploie ce pod dans notre namespace : `test-rbac` avec la commande suivante : 

```bash
kubectl apply -f mon-pod.yaml
```

![Création du pod au sein de notre namespace : test-rbac](screen/Session3/Creation-pod-dans-test-rbac-namespace.png)

Notre **pod** a bien été créé.

Pour afficher les logs de notre pod il faut utiliser la commande suivante : 

```bash
kubectl logs nginx -n test-rbac
```

![Affichage des logs de notre pod](screen/Session3/logs-pods.png)

Création d'un fichier `role-pod-reader.yaml` afin d'avoir un **rôle** capable de lire les pods dans le namespace `test-rbac`. 

On applique le rôle avec la commande suivante : 

```bash
kubectl apply -f role-pod-reader.yaml
```

![Image du déploiement de notre role pour lire les pods](screen/Session3/Deploiement-role-lire-pod.png)

Notre rôle est bien créée et maintenant nous voulons l'afficher : 