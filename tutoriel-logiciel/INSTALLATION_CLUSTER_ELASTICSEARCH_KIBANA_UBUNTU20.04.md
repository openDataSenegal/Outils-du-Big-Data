# Introduction
Ce tutoriel explique l'installation d'un cluster elasticsearch et d'une interface KIBANA sur des machines équipés d'un système d'exploitation UBUNTU 20.04
ici, on se limitera à l'installation d'un cluster elasticsearch composé de deux noeuds elasticsearchs et d'une interface Kibana

# Téléchargement des binaires
On va commencer par télécharger les binaires des logiciels Elasticsearch et Kibana de la verion 8.3.3 . Il faut remarquer que les versions de Kibana et d'Elasticsearch utilisées doivent être les mêmes pour avoir un très bon fonctionnement.
Il faut créer un répertoire nommé par exemple elasticsearch-kibana et se placer en ligne de commande dans ce répertoire. On pourra alors télécharger les binaires des logiciels avec les commandes ci-dessous :
```
  wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.3.3-linux-x86_64.tar.gz
  wget https://artifacts.elastic.co/downloads/kibana/kibana-8.3.3-linux-x86_64.tar.gz
```
Il faut ensuite décompresser les binaires avec les commandes suivantes :
```
  tar zxvf elasticsearch-8.3.3-linux-x86_64.tar.gz
  tar zxvf kibana-8.3.3-linux-x86_64.tar.gz
```
# Configuration Elasticsearch
## Configuration HTTPS Elasticseach
la version de elasticsearch utilisée ici, autogenère automatiquement des certificats https qu'on peut trouver dans le repertoire elasticsearch-8.3.3/config/certs.
Donc, aucune configuration HTPPS n'est nécessaire à moins de vouloir configurer soit même de nouveaux certificat HTTPS.
## Configuration du TAS du JVM Elasticsearch
Il faut configurer le TAS du JVM à 4giga. Pour cela, il faut se rendre dans le fichier elasticsearch-8.3.3/config/jvm.options et décommenter les lignes 32 et 33 (-Xms4g et -Xmx4g)
## configuration du nom du cluster
On peut configurer le nom de notre Cluster en mettant un nom plus parlant pour notre projet. Pour cela, se rendre dans le fichier elasticsearch-8.3.3/config/elasticsearch.yml et décommanter la ligne 17 et changer le nom du claster. 
```
  cluster.name: nom-de-mon-cluster
```
# Lancement du cluster Elasticsearch
Une fois, cela fait, on peut tester si notre elasticsearch fonction correctement en se plaçant en ligne de commande dans le répertoire elasticsearch-8.3.3 et en lançant la commande :
```
  ./bin/elasticsearch
```
Alors, vous pouvez vérifier si le logiciel a bien été installer en lacant à partir d'une autre ligne de commande la commande ci-dessous :
```
  curl --insecure -X GET -u elastic:mots-depasse "https://localhost:9200/"
```
Le mot de passe et le username ont été configuré automatiquement par elasticsearch. Lors du lancement du cluster pour la première fois, vous verrez dans le terminal les authentifications de l'utilisateur elastic

```
  The generated password for the elastic built-in superuser is:
  <password>

  The enrollment token for Kibana instances, valid for the next 30 minutes:
  <enrollment-token>

  The hex-encoded SHA-256 fingerprint of the generated HTTPS CA DER-encoded certificate:
  <fingerprint>

  You can complete the following actions at any time:
  Reset the password of the elastic built-in superuser with
  'bin/elasticsearch-reset-password -u elastic'.

  Generate an enrollment token for Kibana instances with
  'bin/elasticsearch-create-enrollment-token -s kibana'.

  Generate an enrollment token for Elasticsearch nodes with
  'bin/elasticsearch-create-enrollment-token -s node'.
```
# Configuration Kibana
## Création des certificats HTTPS destinés à Kibana
La prémière choses à faire pour la configuration de Kibana est la génération des certificats HTTPS. Pour se faire, il faut se rendre au repertoire de Elasticsearch et executer effectuer les operations Suivantes:
* Mot de passe du conteneur http.p12 : executer la commande ci-dessous pour obtenir le mot de passe de http.
 ```
    ./bin/elasticsearch-keystore show xpack.security.http.ssl.keystore.secure_password
 ```
* Génération des certificats destinés à Kibana : Utiliser le mot de passe généré précédement pour obtenir les certificats en executant les commandes ci-dessous:
 ```
    openssl pkcs12 -in /chemin_complet/elasticsearch-8.3.3/config/certs/http.p12 -out client.crt -clcerts -nokeys
    openssl pkcs12 -in /chemin_complet/elasticsearch-8.3.3/config/certs/http.p12 -out client.key -nocerts -nodes
 ```
Utilisé le mot de passe généré précédement quand on vous demandera de fournir le mot de passe.
Prendre 
## Configuration de la connexion HTTPS entre les navigateurs et Kibana
Pour la configuration de Kibana, il faut commencer d'abord par chiffrer les communications entre Kibana et les navigateurs. Pour chiffrer les communications entre Kibana et les navigateurs, il faut activer le chiffrage HTTPS; Pour ce faire, il faut créer le répertoire kibana-8.3.3-linux-x86_64/config/certs/ et copier les certificats HTTPS qui sont destinés à Kibana. Il faut ensuite modifier dans le fichier kibana-8.3.3-linux-x86_64/config/kibana.yml les lignes 37, 38, 39 pour le transformer ainsi:
```
  server.ssl.enabled: true
  server.ssl.certificate: config/certs/client.cer 
  server.ssl.key: config/certs/client.key
```
Ou clien.cer et client.key sont les certificats HTTPS destinés à Kibana.

## Configuration des Tokens Et lancement de Kibana
Une fois les modifications ci-dessus effectuées, il faut maintenant lancer Kibana en executant la commande ci-dessous à partir du repertoire kibana-8.3.3-linux-x86_64 :
```
  ./bin/kibana
```
Cette commande prendra quelques minutes pour se lancer complètement.

Une fois l'exécution de cette commande terminée, il faut ouvrir dans un navigateur l'URL suivant [https://localhost:5601](https://localhost:5601/?code=723807). Alors, il nous demandera de fournir un Token.
Il faut repartir dans le répertoire d'Elasticsearch et lancer la commande :
```
  ./bin/elasticsearch-create-enrollment-token -s kibana
```
Cela va générer un Token en ligne de commande qu'il faudra copié et le mettre dans l'interface Kibana.
Ensuite Il faut cliquer sur le bouton "Configure Elastic".
Après quelques secondes, Kibana va nous rediriger vers la page d'authentification. On pourra s'authentifier avec les identifiants elastcisearch


## Configuration de la connexion HTTPS Entre Kibana et Elasticsearch
Pour chiffrer les communications entre Kibana et Elasticsearch, il faut :
* Ajouter la ligne  ci-dessous dans le noeud master elasticsearch sur lequel Kibana va faire ces requetes 
```
  xpack.security.http.ssl.client_authentication: required
```
Cette Ligne est à ajouter dans le fichier de configuration elasticsearch-8.3.3/config/elasticsearch.yml de elasticsearch.
Normallement avec cette version de Kibana et de elasticsearch. Le chiffrage des communications entre Kibana et elasticsearch est activé par défaut.
Donc, ajouter cette ligne sur la fiche de configuration d'Elasticsearch sera suffisant.


## Configuration de la connexion entre Elasticsearch et Kibana
Il faut ouvrir le fichier kibana-8.3.3-linux-x86_64/config/kibana.yml et appliquer les modifications suivantes :
Sur la ligne 170, changer elasticsearch.hosts comme suite :
  ```
      elasticsearch.hosts: ['https://localhost:9200', 'url_master2', 'url_master3']
  ```
 ou les URLS à l'intérieur des [] sont les URLs de la liste des serveurs masters du cluster Elastcisearch
 Changer aussi l'adresse les adresses IP qui se trouvent dans la ligne 173 xpack.fleet.outputs par :
 ```
      xpack.fleet.outputs: [{id: fleet-default-output, name: default, is_default: true, is_default_monitoring: true, type: elasticsearch, hosts: ['https://localhost:9200', 'url_master2', 'url_master3'], ca_trusted_fingerprint: ac21f15a8db62216e1c92763fcebaa73609ecddbab06d2075e814d15de879668}]
  ```
Redémarrer en suite Kibana et Elasticsearch.
# Ajouter un nouveau Noeuds

Pour ajouter un nouveau noeuds Elasticsearch sur une autre machine. Il faut télécharger la meme version des binaires utilisée pour le noeud master elasticsearch. Dans notre cas, on peut le faire sur notre nouvelle machine avec la commande ci-dessous:
```
  wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.3.3-linux-x86_64.tar.gz
```
Il faut ensuite décompresser les binaires avec les commandes suivantes :
```
  tar zxvf elasticsearch-8.3.3-linux-x86_64.tar.gz
```
## Configuration du noeud Elasticsearch
### Configuration HTTPS du noeud Elasticseach
la version de elasticsearch utilisée ici, autogenère automatiquement des certificats https qu'on peut trouver dans le repertoire elasticsearch-8.3.3/config/certs.
Donc, aucune configuration HTPPS n'est nécessaire à moins de vouloir configurer soit même de nouveaux certificat HTTPS.
### Configuration du TAS du JVM Elasticsearch
Il faut configurer le TAS du JVM à 4giga. Pour cela, il faut se rendre dans le fichier elasticsearch-8.3.3/config/jvm.options et décommenter les lignes 32 et 33 (-Xms4g et -Xmx4g)
### Création d'un token d'inscription pour ce noeud
Pour ajouter un nouveau à notre cluster, il nous faudra créer un token pour ce nœud. Pour cela, il faut se rendre dans le répertoire du nœud master ou de n'importe quel nœud du cluster et exécuter la commande ci-dessous :
```
  ./bin/elasticsearch-create-enrollment-token -s node
```
Cette commande va générer un token.
Il faut ensuite se rendre dans le répertoire du nœud et exécuter la commande ci-dessous pour le premier lancement du noeud:
```
  ./bin/elasticsearch --enrollment-token 'enrollment-token'
```
Ou 'enrollment-token' est le token généré ci-dessus.
Cette opération va finaliser le lancement de notre nouveau nœud.

Pour les prochains lancements du nœud, il faudra juste exécuter la commande ci-dessous :
```
  ./bin/elasticsearch
```















