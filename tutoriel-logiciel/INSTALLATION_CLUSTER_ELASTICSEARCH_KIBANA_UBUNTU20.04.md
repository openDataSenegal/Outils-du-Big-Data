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


Une fois, cela fait, on peut tester si notre elasticsearch fonction correctement en se plaçant en ligne de commande dans le répertoire elasticsearch-8.3.3 et en lançant la commande :
```
  ./bin/elasticsearch
```
Alors, vous pouvez vérifier si le logiciel a bien été installer en lacant à partir d'une autre ligne de commande la commande ci-dessous :
```
  curl --insecure -X GET -u elastic:4iDeAhwiNvjaU2nAucn8 "https://localhost:9200/"
```
