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
La version d'Elasticsearch utilisée ici, auto générée automatiquement des certificats HTTPS qu'on peut trouver dans le repertoire elasticsearch-8.3.3/config/certs.
Donc, aucune configuration HTPPS n'est nécessaire à moins de vouloir configurer soit même de nouveaux certificat HTTPS.
## Configuration du TAS du JVM Elasticsearch
Il faut configurer le TAS du JVM à 4giga. Pour cela, il faut se rendre dans le fichier elasticsearch-8.3.3/config/jvm.options et décommenter les lignes 32 et 33 (-Xms4g et -Xmx4g).
## configuration du nom du cluster
On peut configurer le nom de notre Cluster en mettant un nom plus parlant pour notre projet. Pour cela, se rendre dans le fichier elasticsearch-8.3.3/config/elasticsearch.yml et décommenter la ligne 17 et changer le nom du cluster. 
```
  cluster.name: nom-de-mon-cluster
```
## Changement de l'adresse IP
Il faut changer l'adresse ip d'elasticsearch pour qu'il écoute avec l'adresse ip de notre VM. Pour se faire, il faut ouvrir le fichier elasticsearch.yml qui se trouve dans le répertoire config de elasticsearch et changer l'adresse ip de "network.host" par "l'adresse ip de la machine" et le port "http.port" avec 9200 en le décommentant
Si lors de la modification de "network.host" par "0.0.0.0", le lancement de ./bin/elasticsearch crash, il faut penser à augmenter le vm.max_map_count de 65 000 à 260 000 avec la commande
```
   sysctl -w vm.max_map_count=262144
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
La première chose à faire pour la configuration de Kibana est la génération des certificats HTTPS. Pour ce faire, il faut se rendre au répertoire d'Elasticsearch et executer les opérations Suivantes:
* Mot de passe du conteneur http.p12 : exécuter la commande ci-dessous pour obtenir le mot de passe de http.
 ```
    ./bin/elasticsearch-keystore show xpack.security.http.ssl.keystore.secure_password
 ```
* Génération des certificats destinés à Kibana : utiliser le mot de passe généré précédemment pour obtenir les certificats en exécutant les commandes ci-dessous :
 ```
    openssl pkcs12 -in /chemin_complet/elasticsearch-8.3.3/config/certs/http.p12 -out client.crt -clcerts -nokeys
    openssl pkcs12 -in /chemin_complet/elasticsearch-8.3.3/config/certs/http.p12 -out client.key -nocerts -nodes
 ```
Utiliser le mot de passe généré précédemment quand on vous demandera de fournir le mot de passe.
## Configuration de la connexion HTTPS entre les navigateurs et Kibana
Il faut maintenant chiffrer les communications entre Kibana et les navigateurs. Pour chiffrer les communications entre Kibana et les navigateurs, il faut activer le chiffrage HTTPS; Pour ce faire, il faut créer le répertoire kibana-8.3.3-linux-x86_64/config/certs/ et copier les certificats HTTPS générés précédents (client.crt, client.key) qui sont destinés à Kibana. Il faut ensuite modifier dans le fichier kibana-8.3.3-linux-x86_64/config/kibana.yml les lignes 37, 38, 39 pour le transformer ainsi:
```
  server.ssl.enabled: true
  server.ssl.certificate: config/certs/client.crt 
  server.ssl.key: config/certs/client.key
```
Ou clien.crt et client.key sont les certificats HTTPS destinés à Kibana.
## Accessibilité de Kibana à l'exterieur
Pour que Kibana soit accessible à l'exterieur, il faut configurer l'adresse Ip de kibana à 0.0.0.0 ou bien à l'adresse IP de la VM qui heberge kibana. Pour cela, il faut décommenter et changer la valeur de server.host de la ligne 10 à "0.0.0.0":
```
    server.host: "0.0.0.0"
```

## Configuration des Tokens Et lancement de Kibana
Une fois les modifications ci-dessus effectuées, il faut maintenant lancer Kibana en exécutant la commande ci-dessous à partir du repertoire kibana-8.3.3-linux-x86_64 :
```
  ./bin/kibana
```
Cette commande prendra quelques minutes pour se lancer complètement.
Une fois l'exécution de cette commande terminée, il faut ouvrir dans un navigateur l'URL suivant [https://localhost:5601](https://localhost:5601/?code=723807). Alors, il nous demandera de fournir un Token.
Il faut repartir dans le répertoire d'Elasticsearch et lancer la commande :
```
  ./bin/elasticsearch-create-enrollment-token -s kibana
```
Cela va générer un Token en ligne de commande qu'il faudra copier et le mettre dans l'interface Kibana.
Ensuite, il faut cliquer sur le bouton "Configure Elastic".
Après quelques secondes, Kibana va nous rediriger vers la page d'authentification. On pourra s'authentifier avec les identifiants elastcisearch

## Configuration de la connexion HTTPS Entre Kibana et Elasticsearch
Pour chiffrer les communications entre Kibana et Elasticsearch, il faut :
* Ajouter la ligne  ci-dessous dans le noeud master elasticsearch sur lequel Kibana va faire ces requetes 
```
  xpack.security.http.ssl.client_authentication: required
```
Cette Ligne est à ajouter dans le fichier de configuration elasticsearch-8.3.3/config/elasticsearch.yml de elasticsearch.
Normalement avec cette version de Kibana et de elasticsearch. Le chiffrage des communications entre Kibana et elasticsearch est activé par défaut.
Donc, ajouter cette ligne sur la fiche de configuration d'Elasticsearch sera suffisant.


## Configuration de la connexion entre Elasticsearch et Kibana
Il faut ouvrir le fichier kibana-8.3.3-linux-x86_64/config/kibana.yml et appliquer les modifications suivantes :
Sur la ligne 170, changer elasticsearch.hosts comme suite :
  ```
      elasticsearch.hosts: ['https://localhost:9200', 'url_master2', 'url_master3']
  ```
 Ou les URLS à l'intérieur des [] sont les URLs de la liste des serveurs masters du cluster Elastcisearch
Changer aussi l'adresse les adresses IP qui se trouvent dans la ligne 173 xpack.fleet.outputs par :
 ```
      xpack.fleet.outputs: [{id: fleet-default-output, name: default, is_default: true, is_default_monitoring: true, type: elasticsearch, hosts: ['https://localhost:9200', 'url_master2', 'url_master3'], ca_trusted_fingerprint: ac21f15a8db62216e1c92763fcebaa73609ecddbab06d2075e814d15de879668}]
  ```
Redémarrer en suite Kibana et Elasticsearch. Pour démarrer Kibana, il faut exécuter la commande ci-dessous en se plaçant dans le répertoire kibana-8.3.3:
```
    ./bin/kibana 
```
Alors Kibana sera accessible à partir d'une interface web avec le lien suivant :
```
    https://adresse_ip_de_la_machine:5601 
```
# Ajouter un nouveau Nœud

Pour ajouter un nouveau noeuds Elasticsearch sur une autre machine. Il faut télécharger la meme version des binaires utilisée pour le noeud master elasticsearch. Dans notre cas, on peut le faire sur notre nouvelle machine avec la commande ci-dessous:
```
  wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.3.3-linux-x86_64.tar.gz
```
Il faut ensuite décompresser les binaires avec les commandes suivantes :
```
  tar zxvf elasticsearch-8.3.3-linux-x86_64.tar.gz
```
## Configuration du nœud Elasticsearch
### Configuration HTTPS du nœud Elasticseach
la version de elasticsearch utilisée ici, autogenère automatiquement des certificats https qu'on peut trouver dans le repertoire elasticsearch-8.3.3/config/certs.
Donc, aucune configuration HTPPS n'est nécessaire à moins de vouloir configurer soit même de nouveaux certificat HTTPS.
### Configuration du TAS du JVM Elasticsearch
Il faut configurer le TAS du JVM à 4giga. Pour cela, il faut se rendre dans le fichier elasticsearch-8.3.3/config/jvm.options et décommenter les lignes 32 et 33 (-Xms4g et -Xmx4g)
### configuration du nom du cluster
Il faut configurer le nom de notre Cluster en mettant le même nom identique que celui utilisé pour le nœud master. Pour cela, se rendre dans le fichier elasticsearch-8.3.3/config/elasticsearch.yml et décommenter la ligne 17 et changer le nom du cluster. 
```
  cluster.name: nom-de-mon-cluster
```
### Création d'un token d'inscription pour ce nœud
Pour ajouter un nouveau à notre cluster, il nous faudra créer un token pour ce nœud. Pour cela, il faut se rendre dans le répertoire du nœud master ou de n'importe quel nœud du cluster et exécuter la commande ci-dessous :
```
  ./bin/elasticsearch-create-enrollment-token -s node
```
Cette commande va générer un token.
Il faut ensuite se rendre dans le répertoire du nœud et exécuter la commande ci-dessous pour le premier lancement du nœud:
```
  ./bin/elasticsearch --enrollment-token 'enrollment-token'
```
Ou 'enrollment-token' est le token généré ci-dessus.
Cette opération va finaliser le lancement de notre nouveau nœud.

Pour les prochains lancements du nœud, il faudra juste exécuter la commande ci-dessous :
```
  ./bin/elasticsearch
```
# création et Activation du systemd pour elasticsearch
to do

# création et Activation du systemd pour Kibana
to do

### Remarque : 
- Si le nœud est installé dans une autre machine, penser à changer les adresses de 
```
    transport.host:: add_ip_ou_host_du_master
```
en mettant les adresses des masters
- Pour lancer le serveur en arriere plan, il faut executer la commande suivante:
```
    nohup ./bin/elasticsearch > nohup_logs.out -d
```
- Pour lancer elasticsearch, il faut utiliser un utilisateur qui n'est pas admin. Pour créer un utilisateur sous linux, il faut executer la commande ci-dessous:
```
    sudo adduser elasticsearchrunner
```
Pour donner à cet utilisateur un mot de passe, il faut exécuter la commande ci-dessous :
```
   sudo passwd elasticsearchrunner
```
Pour donner à cet utilisateur le droit d'executer le fichier .bin/elasticsearch, il doit avoir les droits necessaires et pour ca, il faut executer la commande ci-dessous:
```
    sudo chown -R elasticsearchrunner:elasticsearchrunner /opt/elasticsearch-8.3.3
```








