# Introduction
Ce tutoriel explique l'installation dans une machine équipée d'un système d'exploitation UBUNTU20.04 de [MinIO](https://github.com/minio/minio) qui est un 
systeme de stockage d'objets hautes performances publié sous la licence publique générale GNU Affero v3.0. Il est compatible API avec le service de 
stockage en nuage Amazon S3. Utilisez MinIO pour créer une infrastructure hautes performances pour les charges de travail d'apprentissage automatique, 
d'analyse et de données d'application.
Cette définition de MinIo a été prise dans le github.

Dans ce tutoriel, nous allons supposer que nous disposons de deux machines équipées du système d'exploitation UBUNTU20.04 : machine1 (IP = adress_ip_1) et 
machine2 (IP = adress_ip_2)

# Installation du Nœud MinIO sur la machine1
## Configuration du DNS

La premiere chose à faire est d'assigner un nom de domaine et de configurer le DNS de chaque machine. En effet, MinIO aura besoin des noms de 
domaine pour pouvoir fonctionnement correctement.
Nous allons assigner le nom de domaine minio1.panongbene.com à la machine1 et minio2.panongbene.com à la machine 2.
Pour configurer le DNS, il faut éditer le fichier /etc/hosts. Pour cela, executer la commande suivante:
```
  nano /etc/hosts
```
et ajouter les lignes ci-dessous dans ce fichier :
```
  adress_ip_1 minio1.panongbene.com
  adress_ip_2 minio2.panongbene.com
```
Enregistrer et fermer le fichier en faisant ctrl+s et ctrl+x. On pourra vérifier si les noms de domaines ont été bien pris en charge en exécutant la 
commande ci-dessous.
```
  ping -c 4 minio1.panongbene.com
```

## Installation de MinIO en utilisant le binaire

Il faut ensuite télécharger le binaire de MinIO destiné à Linux et le mettre dans le répertoire /usr/local/bin/. Pour cela exécuter la commande ci-dessous:
```
  wget https://dl.min.io/server/minio/release/linux-amd64/minio
  chmod +x minio
  sudo mv minio /usr/local/bin/
```




















