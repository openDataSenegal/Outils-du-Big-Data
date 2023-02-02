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
## SystemD
Il faut créer ensuite un systemD pour permettre le démarrage et l'allumage en utilisant le systemD. Pour cela, il faut créer un fichier nommé minio.service dans le répertoire/etc/systemd/system et le remplir comme ci-dessous:
```
  [Unit]
  Description=MinIO
  Documentation=https://min.io/docs/minio/linux/index.html
  Wants=network-online.target
  After=network-online.target
  AssertFileIsExecutable=/usr/local/bin/minio

  [Service]
  WorkingDirectory=/usr/local

  User=minio-user
  Group=minio-user
  ProtectProc=invisible

  EnvironmentFile=-/etc/default/minio
  ExecStartPre=/bin/bash -c "if [ -z \"${MINIO_VOLUMES}\" ]; then echo \"Variable MINIO_VOLUMES not set in /etc/default/minio\"; exit 1; fi"
  ExecStart=/usr/local/bin/minio server $MINIO_OPTS $MINIO_VOLUMES

  # Let systemd restart this service always
  Restart=always

  # Specifies the maximum file descriptor number that can be opened by this process
  LimitNOFILE=65536

  # Specifies the maximum number of threads this process can create
  TasksMax=infinity

  # Disable timeout logic and wait until process is stopped
  TimeoutStopSec=infinity
  SendSIGKILL=no

  [Install]
  WantedBy=multi-user.target

  # Built for ${project.name}-${project.version} (${project.name})
```
## Définition des variables d'environnement 
Il faut définir les variables d'environnement qui seront utilisées par systemd:
```
    export Minio PassWord = mot_de_passeçadmin
    export MINIO_ROOT_USER=user_name_compte_admin
    export MINIO_ROOT_PASSWORD=mot_de_pass_compte_admin
    export MINIO_VOLUMES=path_du_repertoire_ou_seront_stoques_les_donnes
    export MINIO_OPTS="--console-address :9001"
    export MINIO_SERVER_URL="http://adress_ip_1:9000"
```
## Démarage de MinIO

Une fois les variables d'environnement configuré, on peut démarrer MinIO en exécutant la commande du systemD suivante :

```
  systemctl start minio.service
```
Alors MinIO sera fonctionnel et vous pourrez y accéder en utilisant l'URL adress_ip_1 sur le port 9000 et utilisant les authentifications définies dans les variables d'environnement.

## Configuration du HTTPS

Pour configurer le HTTPS, il faut générer un certificat valide par rapport à l'url utilisé pour MinIO. Si on a auto-généré notre certificat, alors on aura besoin de le signer nous-même pour cela, nous allons utiliser l'outil [certgen](https://github.com/minio/certgen/releases).

Télécharger et installer certgen en exécutant les commandes suivantes : 
```
  wget https://github.com/minio/certgen/releases/download/v1.2.0/certgen_1.2.0_linux_amd64.deb
  sudo dpkg -i certgen_1.2.0_linux_amd64.deb
```
Il faut ensuite générer les certificats avec les commandes ci-dessous:
```
   sudo certgen -host minio1.panongbene.com,adress_ip_1
```

Une fois les certificats https générés, il faut changer la configuration de la variable d'environnement MINIO_OPTS :
```
    export MINIO_OPTS="--certs-dir /chemin_vers_les_certs/.minio/certs --console-address :9001"
```
On peut alors relancer le MinIO en utilisant les commandes ci-dessous.

```
  systemctl daemon-reload
  systemctl stop minio.service
  systemctl start minio.service
```
Alors le serveur MinIO sera accessible en https à l'adresse https://adress_ip_1:9000.

Pour voir les logs d'un systemD faire :
```
  journalctl -f -u minio.service
```
# Installation du Nœud MinIO sur la machine2

to do

















