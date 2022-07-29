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
la version de kibana et de elasticsearch utilisée ici autogenère automatiquement des certificats https qu'on peut trouver dans le repertoire elasticsearch-8.3.3/config/certs.
Donc, aucune configuration HTPPS n'est nécessaire à moins de vouloir configurer soit même de nouveaux certificat HTTPS.
## Configuration du TAS du JVM Elasticsearch
Il faut configurer le TAS du JVM à 4giga. Pour cela, il faut se rendre dans le fichier elasticsearch-8.3.3/config/jvm.options et décommenter les lignes 32 et 33 (-Xms4g et -Xmx4g)
## configuration du nom du cluster
On peut configurer le nom de notre JVM en mettant un nom plus parlant pour notre projet. Pour cela, se rendre dans le fichier elasticsearch-8.3.3/config/elasticsearch.yml et décommanter la ligne 17 et changer le nom du claster. 
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
## Configuration de la connexion entre Elasticsearch et Kibana
Pour que Kibana accède à Elasticsearch, il faut ouvrir le fichier kibana-8.3.3-linux-x86_64/config/kibana.yml et appliquer les modifications suivantes :
Décommenter et modifier la ligne 43 comme ci-dessous
  ```
      elasticsearch.hosts: ['https://localhost:9200', 'url_master2', 'url_master3']
  ```
  ou les URLS à l'intérieur des [] sont les URLs de la liste des serveurs masters du cluster Elastcisearch
## Configuration de la connexion HTTPS entre les navigateurs et Kibana
Pour chiffrer les communications entre Kibana et les navigateurs, il faut activer le chiffrage https; Pour ce faire, il faut créer le repertoire kibana-8.3.3-linux-x86_64/config/certs/ et copier les certificats https qui sont destinés à Kibana. Il faut ensuite modifier dans le fichier kibana-8.3.3-linux-x86_64/config/kibana.yml les lignes 37, 38, 39 pour le transformer ainsi: 
```
  server.ssl.enabled: true
  server.ssl.certificate: config/certs/client.cer 
  server.ssl.key: config/certs/client.key
```
ou clien.cer et client.key sont les certificats https destinés à Kibana
## Configuration de la connexion HTTPS Entre Kibana et Elasticsearch
Pour chiffrer les communications entre Kibana et Elasticsearch, il faut :
* Ajouter la ligne  ci-dessous dans le noeud master elasticsearch sur lequel Kibana va faire ces requetes 
```
  xpack.security.http.ssl.client_authentication: required
```
Cette Ligne est à ajouter dans le fichier de configuration elasticsearch-8.3.3/config/elasticsearch.yml de elasticsearch.

## Configuration des Tokens

