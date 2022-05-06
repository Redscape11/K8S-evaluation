# Réponse

J'ai commencé par créer 1 pod et 1 service pour chaque container (node-redis et redis)

J'essaie de connecter les 2 container en premier lieu.

Utiliser des pods simple me permet de simplifier la tâche et une fois que ça marche, je produis un deployment avec la même config

J'ai utilisé une commande qui me permet d'explorer le code du fichier du serveur :
kubectl exec psyduck11-node-redis-new -- cat main.js


Au final j'ai créé :
- 2 pods 
- 4 pods node-redis avec le deployment
- 2 services


PODS : 
- psyduck11-node-redis-new
- psyduck11-node-redis-new-656567bc4b-2chwl
- psyduck11-node-redis-new-656567bc4b-4q7fq
- psyduck11-node-redis-new-656567bc4b-7txgx
- psyduck11-node-redis-new-656567bc4b-bvgnl
- psyduck11-redis-new

SERVICES :
- psyduck11-node-redis-new
- psyduck11-redis-new



## redis
### redis/psyduck11-redis-pod.yaml
- crée 1 pod psyduck11-redis-new
- la propriété env n'est pas nécessaire, j'ai oublié de l'enlever
- il se base sur l'image redis, kubectl va directement pull cette image depuis le hub en ligne

### redis/psyduck11-redis-service.yaml
- crée 1 service psyduck-redis-new
- redis utilise le port 6379 par défault, d'oû port et targetPort sont à 6379


## node-redis
### redis/psyduck11-node-redis-pod.yaml
- crée 1 pod psyduck11-node-redis-new
- env.PORT=3000 permet de définir le port du serveur express
- env.REDIS_URL permet de dire à l'app que redis se trouve à cette URL
- l'adresse IP choisie est le Cluster-IP du service de redis

### redis/psyduck11-node-redis-service.yaml
- crée un service psyduck11-node-redis-new
- comme notre container utilise le port 3000, alors on map le port interne à 3000, on décide aussi de mettre le port 3000 pour le port externe au service

### redis/psyduck11-node-redis-deployment.yaml
- crée 4 pods psyduck11-node-redis-new
- config similaire au redis/psyduck11-node-redis-pod.yaml








Ensuite j'ai créé 1 service appelé psyduck11-redis-new avec redis/psyduck11-redis-service.yaml

Dans le fichier redis-pod.yaml, j'ai oublié d'enlever les variables d'env, elles ne servent pas pour redis

- node-redis
J'ai fait pareil pour node-redis 




# Examen

## Prérequis

Récupérer le fichier kubeconfig.yml

```SH
export KUBECONFIG=<absolute-path-to>/kubeconfig.yml
```

Vous allez utiliser un cluster distant, il ne faut donc pas utiliser de commandes commençant par `minikube`.

## Tâches

Pour chacun de vos déploiements, pods, services (ou autres), veuillez ajouter votre nom (ou un identifiant que vous m'envoyez par slack) pour permettre d'identifier votre travail.

C'est également important pour les `labels selector` pour éviter les collisions entre le travail de deux élèves.
(un service qui cible des pods de deux personnes).

### Déploiement

Créer 2 deploiements (toutes les images sont fournis) :

- redis (une base https://redis.io/)
- cloud.canister.io:5000/arhturescriou/node-redis (un serveur que j'ai codé)

Cette image est privé il faut donc ajouter l'utilisation du secret pour l'utiliser :

```yaml
spec:
  imagePullSecrets:
    - name: regcred
```

Le serveur (node-redis) nécessite des variables d'environment pour fonctionner

```yaml
env:
  - name: PORT
    value: <port>
  - name: REDIS_URL
    value: redis://<address-of-base>
```

### Service

Créer un service pour communiquer avec le serveur (node-redis) depuis l'extérieur.

### Pods

Un fois qu'un serveur fonctionne augmentez le nombre de pods (node-redis), entre 3 et 5. Et vérifier que cela fonctionne toujours.

## Rendu

Veuillez regrouper tous vos fichiers yaml de déploiement (et ou commande lancées en bash) dans un repository git muni d'un readme.md.

Pousser le repository en ligne (github, gitlab etc).
Et m'envoyer le lien sur slack (cela peut être fait en debut d'examen, je regarderai la dernière version poussée)

## Commandes utiles

Visualiser les ressources déployées

```bash
kubectl get pods
kubectl get services
kubectl get deployments
```

Récupérer les log d'un pod

```bash
kubectl logs <pod-id>
```

Utiliser un fichier yaml, dans votre cas l'image docker ne devrais pas changer donc il est possible de seulement apply le yaml une fois créé

Pensez à nommer votre yaml avec votre nom (et pas juste pod.yaml, pour éviter les collisions)

```bash
kubectl create -f file.yaml
kubectl delete -f file.yaml
kubectl apply -f file.yaml
```
