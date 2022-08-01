# Introduction
Ce tutoriel explique l'installation d'un cluster Kafka et d'une interface d'administration du cluster sur des machines équipés d'un système d'exploitation UBUNTU 20.04.
Dans ce tutoriel, on expliquera l'installation de la version 3.2.1 de Kafka qui est la version stable actuelle. On se limitera à l'installation d'un cluster Kafka composé de deux nœuds Kafka.

# Mises à jour et Installation de Java
La première chose à faire et la mise à jour de notre système d'exploitation Ubuntu et l'installation ou la mise à jour de Java si cela n'est pas déjà fait sur notre système d'exploitation. Pour cela, il faut executer les commandes ci-dessous:
```
  sudo apt update 
  sudo apt install default-jdk
  java --version 
```
Il faut installer la version 11 du JDK de Java

# Installation de Kafka
## Téléchargeant des binaires de Kafka
Une fois que l'installation de Java est terminée, il faut créer un répertoire nommé par exemple kafka-project. Ensuite, il faut télécharger les binaires de la version 3.2.1 de Kafka en exécutant les commandes suivantes :
```
    wget https://dlcdn.apache.org/kafka/3.2.1/kafka_2.12-3.2.1.tgz
```
Il faut ensuite décomprésser les binaires avec la commande ci-dessous:
```
    tar xzf kafka_2.12-3.2.1.tgz
```
Se rendre en ligne de commande dans le dossier kafka_2.12-3.2.1 et crééer le dossier qui contiendront les données zookeepeer et les logs kafkas. 
```
    mkdir jms-zookeeper-data
    mkdir jms-sorties-logs
```
## Lancement de Zookeeper
La version de kafka téléchargée inclus les binaires du gestionnaire de cluster zookeepeer. Il faut toujours lancer zookeepeer avant de lancer le noeud de kafka. Pour cela, il faut se placer dans le repertoire kafka_2.12-3.2.1 et executer la commande suivante:
```
    ./bin/zookeeper-server-start.sh ./config/zookeeper.properties
```
On peut tester que zookeepeer a bien été lancé en executant la commande ci-dessous:
```
   telnet localhost 2181
```
Cette commande se connecte à zookeepeer qui est ouvert par défaut au port 2181.
## Configuration de zookeepeer
On peut configurer zookeepeer en modifiant les informations contenues dans le fichier /kafka_2.12-3.2.1/config/zookeeper.properties
- Pour changer le port, d'écoute de zookeepeer, il  faut changer la valeur de clientPort qui se trouve à la ligne 18 du fichier kafka_2.12-3.2.1/config/zookeeper.properties et mettre la nouvelle valeur du port 
```
    clientPort=2181
```

- Pour changer le dossier de stockage des données de zookeepeer et utiliser les dossiers qu'on a créés. Il faut changer le chemain dataDir à la ligne 17 du fichier /kafka_2.12-3.2.1/config/zookeeper.properties et mettre le chemin du repertoire ou on veut mettre les données dans notre cas:
```
    dataDir=/chemin/kafka_2.12-3.2.1/jms-zookeeper-data
```

## Lancement du noeud kafka
Une fois que le Zookeepeer est lancé, on peut alors lancer le noeud Kafka en ouvrant un nouveau terminale dans le repertoire /kafka_2.12-3.2.1/ et en executant la commande ci-dessous:
```
   ./bin/kafka-server-start.sh ./config/server.properties
```
## Configuration de Kafka
Pour configurer Kafka, il faut se rendre dans le fichier kafka_2.12-3.2.1/config/server.properties et effectuer les modifications suivantes :
* On peut changer l'id du serveur kafka appelé broker. Chaque nœud du serveur Kafka doit avoir une id différente. Pour changer l'id du broker Kafka, il faut se rendre dans le fichier kafka_2.12-3.2.1/config/server.properties à la ligne 24 et changer la valeur:
```
    broker.id=0
```
* Pour éviter que le producer envoie les messages par lots de 200 avec une latence maximale de 1000 ms qui est la configuration par défaut, il faut ajouter les deux lignes de commandes ci-dessous dans le fichier kafka_2.12-3.2.1/config/server.properties
```
    --batch-size=1
    --timeout=0
```
* Pour que Kafka soit atteignable sur les autres réseaux, il faut décommenter et modifier la ligne 34 du fichier kafka_2.12-3.2.1/config/server.properties de chaque broker en y mettant. Cela va permettre à Kafka d'écouté dans tous les interfaces de notre réseau   
```
    listeners=PLAINTEXT://localhost:9092
```
* Il faut mettre le parametre delete.topic.enable dans le fichier kafka_2.12-3.2.1/config/server.properties à true pour permettre la suppression des topics par l'outil d'administration. Si ce parametre n'est pas mis à true, une suppression d'un topics n'aura aucun effet sur le topic Pour cela, il faut ajouter la ligne ci-dessous dans le fichier kafka_2.12-3.2.1/config/server.properties.
```
    delete.topic.enable=true
```
* Il faut spécifier le chemain de sortie de logs de kafka en changant la valeur de log.dirs qui se trouvent dans le fichier kafka_2.12-3.2.1/config/server.properties à la ligne 62. Il faut mettre le chemin complet de stockage des logs, par exemple:
```
    /mnt/c/Users/jmsawadogo/testKafKaZookeper/kafka_2.12-3.2.1
```
* Il faut spécifier aussi le nombre le delais de rétention des données (en heures) avant qu'elles ne soient supprimer par Kafka. La valeur par défaut est de 168 heures soient une semaine. Pour cela il faut modifier la valeur de log.retention.hours qui se trouvent à la ligne 105 du fichier kafka_2.12-3.2.1/config/server.properties. 
```
    log.retention.hours=168
```
* Si Zookeeper est installé dans une autre machine (c'est le cas notament pour certains noeuds quand on utilise un cluster avec plusieurs noeuds), il faut spécifier l'accès à zookeepeer en modifiant la ligne 125 du fichier kafka_2.12-3.2.1/config/server.properties
```
    zookeeper.connect=localhost:2181
    zookeeper.connection.timeout.ms=6000
```
## Configuration HTTPS de Kafka

#### Génération des certificats HTTPS (lien)[https://www.ibm.com/docs/fr/qsip/7.4?topic=options-configuring-apache-kafka-enable-client-authentication]
La premiere chose à faire pour chiffrer les communications de Kafka est la génération des certificats. Pour se faire, il faut executer la commande ci-dessous dans une ligne de commande:
```
    keytool -keystore server.keystore.jks -alias sawadogo_kafka_serveur -validity 365 -genkey -keyalg RSA
```
Définir un mot de passe et completer les autres informations. Cette commande va nous générer un fichier server.keystore.jks
```
    openssl req -new -x509 -keyout ca-key -out ca-cert -days 365
```
Cette commande va générer les fichiers ca-cert   ca-key
```
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file /mnt/c/Users/jmsawadogo/Desktop/usefulRepo/testKafKaZookeper/kafka_2.12-3.2.1/ca-cert
```
Cette commande va générer le fichier kafka.server.truststore.jks

```
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file /mnt/c/Users/jmsawadogo/Desktop/usefulRepo/testKafKaZookeper/kafka_2.12-3.2.1/ca-cert
```
Cette commande va générer le fichier kafka.client.truststore.jks
```
    keytool -keystore server.keystore.jks -alias sawadogo_kafka_serveur -certreq -file cert-file
```
Cette commande va générer le fichier cert-file

```
openssl x509 -req -CA ca-cert -CAkey ca-key -in cert-file -out cert-signed -days 365 -CAcreateserial
```
cette commande va générer le fichier cert-signed


```
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert
```

```
    keytool -keystore kafka.server.keystore.jks -alias server.hostname -import -file cert-signed
```

```
    keytool -exportcert -keystore kafka.server.keystore.jks -alias server.hostname -file server.hostname.der
```

```
    keytool -keystore kafka.client.keystore.jks -alias client.hostname -validity 365 -genkey
```
```
    keytool -keystore kafka.client.keystore.jks -alias client.hostname -certreq -file client-cert-file
    openssl x509 -req -CA ca-cert -CAkey ca-key -in client-cert-file -out client-cert-signed -days 365 -CAcreateserial
```
```
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert
```

```
keytool -keystore kafka.client.keystore.jks -alias client.hostname -import -file client-cert-signed
```

#### Installation des clés
Une fois les certificats créés, pour activer la communication HTTPS avec Kafka, il faut commencer par ajouter la ligne ci-dessous au fichier kafka_2.12-3.2.1/config/server.properties
```
    security.inter.broker.protocol = SSL
```
En suite, il faut changer le listerners de la ligne 34 en y ajoutant le protocol SSL sur un port different: par exemple, on peut mettre le listeners à :
```
    listeners=PLAINTEXT://:9092,SSL://:9093
```
Il faut aussi ajouter les lignes ci-dessous au meme fichier kafka_2.12-3.2.1/config/server.properties:
```
    ssl.client.auth=required
    ssl.keystore.location=/somefolder/kafka.server.keystore.jks
    ssl.keystore.password=test1234
    ssl.key.password=test1234
    ssl.truststore.location=/somefolder/kafka.server.truststore.jks
    ssl.truststore.password=test1234
```



to do
## Ajouter un nouveau noeud à notre cluster
Pour ajouter un nouveau nœud à notre cluster, il suffit de télécharger la même version de Kafka qui a été téléchargé et de le dézipper dans la machine de destination. Cela se fait avec les commandes ci-dessous dans notre cas :
```
    wget https://dlcdn.apache.org/kafka/3.2.1/kafka_2.12-3.2.1.tgz
    tar xzf kafka_2.12-3.2.1.tgz
```
Il faut ensuite modifier la configuration du nouveau noeud de 

# Installation de Kafka Manager


#### Remarque : (Documentation Kafka)[https://kafka.apache.org/documentation/]
