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
- Pour changer le port, modifier clientPort=2181 en mettant la valeur du port souhaitée.
## Lancement du noeud kafka
Une fois que le Zookeepeer est lancé, on peut alors lancer le noeud Kafka en ouvrant un nouveau terminale dans le repertoire /kafka_2.12-3.2.1/ et en executant la commande ci-dessous:
```
   ./bin/kafka-server-start.sh ./config/server.properties
```
## Configuration de Kafka
to do
## Ajouter un nouveau noeud à notre cluster
to do

