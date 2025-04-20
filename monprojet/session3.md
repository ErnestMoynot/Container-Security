 
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

```bash
kubectl get roles -n test-rbac
```
Cette commande permet d'afficher le rôle avec son nom et la date et l'heure à laquelle il a été créé.

![Affichage du rôle dans notre terminal](screen/Session3/affichage-role.png)

Maintenant on va venir lier ce rôle à un utilisateur **fictif**, `titi` dans notre cas. 

On commence par créer un fichier YAML (rolebinding-pod-reader.yaml) qui nous permet **d'accorder les permissions définies** dans le rôle `pod-reader` à l'utilisateur `titi` : 


![Contenu du fichier rolebinding-pod-reader.yalm](screen/Session3/fichier-yalm-rolebinding.png)

On applique le fichier et on s'assure que `RoleBinding` a bien été créé : 

![Image de l'application du fichier rolebinding-pod-reader.yalm et de la création de RoleBinding](screen/Session3/application-creation-RoleBinding.png)

Création de notre utilisateur ficitf **titi** : 

On commence par copier les **certificats CA** depuis le container `mon-cluster-control-plane` : 

![Image de la copie des CA](screen/Session3/Copie-CA.png)

Génération des **clés** et **certificat** pour notre utilisateur `titi` : 

![Image de génération clés et certificat pour utilisateur titi](screen/Session3/Generation-cle-certificat.png)

On ajoute l'utilisateur `titi` dans le contexte kubeconfig : 

![Image ajout user titi dans le contexte kubeconfig](screen/Session3/Ajout-user-titi.png) 

```bash
kubectl config set-credentials titi --client-certificate=titi.crt --client-key=titi.key
```
Cela va permettre à l'utilisateur `titi` de s'authentifier avec son certificat et sa clé privé pour **intéragir** avec le cluster.

La commande suivante permet de créer un **nouveau contexte Kubernetes** appelé `titi-context` dans notre fichier de configuration kubeconfig. Le contexte spécifie les informations nécessaires pour interagir avec un **cluster Kubernetes**

On bascule de contexte : 

```bash
kubectl config use-context titi-context
```

![Image du basculement de contexte](screen/Session3/Bascule-de-contexte.png)

#### Tester l'accès en tant que titi : 

-Lister les pods : 

```bash
kubectl get pods
```
![Image qui montre que l'utilisateur titi peut lister les pods](screen/Session3/Liste-pods-titi.png)

Cela fonctionne car on a lié le rôle `pod-reader` à l'utilisateur `titi`, ce qui lui donne la permission de lister les **pods** dans le namespace `test-rbac`.

-Créer un pod : 

On va tester de créer un pod avec l'utilisateur `titi`. 

On commence par créer un **fichier YALM pour un nouveau pod** : `nouveau-pod.yaml`

![Image du contenu du nouveau fichier yaml pour créer un pod](screen/Session3/Content-file-nouveau-pod.png)

Maintenant on tente de créer le **pod** avec l'utilisateur `titi` : 

```bash
kubectl apply -f nouveau-pod.yaml
```

![Image erreur création nouveau pod par l'utilisateur titi](screen/Session3/Erreur-creation-pod.png)

On obtient une erreur nous indiquant que l'utilisateur `titi` ne peut pas créer de pod. C'est tout à fait normal car il ne possède qu'un rôle `pod-reader` qui ne lui donne que les permissions de lecture et non de création.

### 3. Scanner un Cluster Kubernetes avec Kube-Bench 

On va effectuer un scan de notre cluster avec l'outil **kube-bench**. 

On commence par créer un fichier `job.yaml` : 

![image du contenu du fichier job?yaml](screen/Session3/ceation-job-yaml.png)

On applique le job avec la commande suivante : 

```bash
kubectl apply -f job.yml
```

`job.batch/kube-bench` a bien été créé. 


### 1. Détection et alerte d'intrusion dans **Kubernetes** avec l'outil `Falco`.

On commence par ajouter le **repo helm**, pour ce faire on utilise la commande suivante : 

```bash
helm repo add falcosecurity https://falcosecurity.github.io/charts
```

![Image ajout du repo helm](screen/Session3/Ajout-repo-helm.png)

Dans un second temps on effectue un **update**.

```bash
helm repo update
```
Cela permet de mettre à jour les informations locales concernant les dépôts helm que nous avons ajoutés (accès aux dernières versions, préventions des conflicts, ...).

Création d'un **namespace** `falco` dans kubernetes : 

```bash
kubectl create ns falco
```

![Image création namespace falco](screen/Session3/Creation-namespace-falco.png)


Maintenant on installe la chart `falco` dans le namespace portant son nom et on active *falcosidekick* afin d'avoir une interface utilisateur : 

```bash
helm -n falco install falco falcosecurity/falco --set falcosidekick.enabled=true --set falcosidekick.webui.enabled=true
```

L'installation de Falco avec Helm dans un namespace dédié (`falco`) et l'activation de *Falcosidekick* permettent **d'isoler les composants de sécurité**, **d'assurer une surveillance** en temps réel des activités suspectes dans le cluster Kubernetes, et de **fournir une interface utilisateur** pour gérer efficacement les alertes de sécurité générées par Falco.

Vérification que les pods sont à l'état `running` : 

![Image montrant que les pods sont à l'état running](screen/Session3/Verification-etat-running.png)

Tous les **pods** sont bien à l'état `running`.

Dans un nouveau **shell** on fait un port forward pour afficher l'UI `http://127.0.0.1:2802` de falco dans notre navigateur : 

```bash
kubectl port-forward svc/falco-falcosidekick-ui 2802:2802 --namespace falco
```
![Image UI de Falco dans le navigateur](screen/Session3/UI-falco.png)

### 2. Falco en pratique

Installation d'un pod avec `kubectl apply -f`.

Pour ça on crée un fichier `mon-pod1.yaml` : 

![Image création fichier mon-pod1.yaml](screen/Session3/Creation-mon-pod1.png)

On applique ce fichier pour créer le pod : 

```bash
kubectl apply -f mon-pod1.yaml
```

On exécute un shell dans le pod afin de générer une alerte consultable via l'UI de falco : 

```bash
terminal shell in container
```


Une alerte a été générée concernant cette action. La priorité de l'alerte est **notice**, et la règle qui a été déclenchée est `terminal shell in container`. Cette règle signale l'exécution d'un terminal shell interactif dans un container, ce qui peut être une activité normale dans certains cas, mais qui mérite une surveillance. 

![Image montrant UI falco pour shell in pod](screen/Session3/Alerte-notice-shell-in-pod.png)

Toujours dans le shell du pod, on génère une requête sur l'API kubernetes : 

```bash
apk add curl
curl -k http://10.96.0.1:80
```

Deux alertes ont été générées concernant cette action. La première, avec une priorité `Critical`, est liée à la règle `drop and execute new binary in container`, déclenchée par **l'installation du binaire curl**. La seconde alerte, avec une priorité `Notice`, est liée à la règle `Contact K8S API server from container`, et elle signale la **tentative de contact avec l'API Kubernetes** via la commande curl.

![Image du centre de contrôle des alertes](screen/Session3/Overall-Falco.png)