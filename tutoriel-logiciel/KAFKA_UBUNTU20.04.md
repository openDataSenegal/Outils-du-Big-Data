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
Il faut installer la version 11 de Java


