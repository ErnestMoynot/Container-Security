# Introduction aux Contenaires et à la Sécurité 

## Activités Pratiques

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