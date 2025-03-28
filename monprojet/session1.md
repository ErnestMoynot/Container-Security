# Introduction aux Contenaires et à la Sécurité 

## Enjeux de Sécurité des Containers : Activités Pratiques

### 1. Lancer un Container simple : 

Voici ce qu'il se produit une fois que l'on a exécuté la commande suivante : 

```bash
  docker run --rm hello-world
```

Dans un premier temps, nous recevons un message d'erreur indiquant qu'aucune image 'hello-world:latest' n'a été trouvée localement. Docker va donc en récupérer une depuis le Docker Hub. Cette image sera ensuite stockée localement sur notre machine. Une fois téléchargée, un conteneur sera créé et exécuté à partir de cette image, affichant le message suivant : **Hello from Docker!**


### 2. Explorer un Container en Interactif : 

On lance un container intéractif basé sur **Alpine**.

```bash
docker run -it --rm alpine sh
```

On accède à un terminal directement dans le container. **Alpine** étant un environnement **Linux** il nous est possible d'exécuter des commandes Linux tel que **ls** ; **pwd** et **whoami**. Celles-ci nous permettent respectivement de : 

- Lister les fichiers et répertoires dans un répertoire donné.

- Affiche le chemin absolu du répertoire dans lequel on se trouve.

- Affiche le nom de l'utilisateur actuellement connecté au terminal (root dans notre cas).

### 3. Analyser les ressources système d'un container : 

Dans cette étape, on cherche à surveiller les ressources utilisées par un container. 
Les deux commandes suivantes permettent le lancement d'un container **nginx** et de surveiller la consommation des ressources du container : 

```bash
docker run -d --name test-container nginx
docker stats test-container
```

En sortie on obtient les statistiques d'utilisation des ressources de notre container. Notamment la consommation du cpu et de la mémoire. 

- La consommation du **CPU** est de **0,00%**.

- L'utilisation de la **mémoire** est de **14,95MB**. 

### 4. Lister les capacités d'un container : 

La commande suivante va permettre de lister les capacités du container **Alpine Linux** : 

```bash
docker run --rm --cap-add=SYS_ADMIN alpine sh -c "cat /proc/self/status"
```
On va faire afficher dans notre terminal le contenu du fichier **/proc/self/status**. 

- **`Name: cat`** → Le nom du processus exécuté **(cat)**.
- **`Uid:    0       0       0       0`** et **`Gid:    0       0       0       0`** → indiquent que le processus s'exécute en tant que **root** dans notre container.
- **`Seccomp: 2`** → signifie que le container utilise un mode de **filtrage strict**.
- **`NoNewPrivs: 0`** → **0** signifie que les processus lancés peuvent obtenir de **nouveaux privilèges**.




## Vulnérabilités et Menaces : Activités Pratiques

### 1. Tester un Container avec des Permissions Elevées : 

La commande suivante permet de lancer, avec Docker, un container avec des privilèges élevés : 

```bash
docker run --rm --privileged alpine sh -c 'echo hello from privileged mode'
```

La commande a fonctionné et a correctement affiché **"Hello from privileged mode"**. Cependant, cette pratique est dangereuse car le conteneur obtient les mêmes droits qu'un utilisateur **root** sur notre machine. Cela lui permettrait d'effectuer des modifications potentiellement dangereuses sur le système, **augmentant ainsi les risques**.