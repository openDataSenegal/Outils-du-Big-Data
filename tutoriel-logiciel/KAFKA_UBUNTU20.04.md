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
On peut ensuite vérifier si Kafka fonctionne très bien en lancant la commande ci-dessous pour voir la liste des topics:
```
  sudo ./bin/kafka-topics.sh --list --bootstrap-server localhost:9092
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
On peut ensuite vérifier si Kafka fonctionne très bien en lancant la commande ci-dessous pour voir la liste des topics:
```
  sudo ./bin/kafka-topics.sh --list --bootstrap-server localhost:9092
```
## Configuration HTTPS de Kafka
La configuration du HHTPS utilisée dans cette partie est la configuration SSL. Cette configuration prend aussi en compte l'authentification par les certificats.
#### Configuration
La premiere chose à faire pour mettre en place le HTTPS sous Kafka est de modifier la configuration précédente.
* Il faut comment par modifier le port d'écoute et de le mettre au port 9093 et changer le PLAINTEXT par SSL, pour ce faire, il faut modifier la valeur du listeners comme suite
```
    listeners=SSL://0.0.0.0:9093
    advertised.listeners=SSL://:9093
```

#### Génération des certificats HTTPS [lien](https://www.ibm.com/docs/fr/qsip/7.4?topic=options-configuring-apache-kafka-enable-client-authentication)
Pour chiffrer les communications de Kafka, il faut ensuite la génération des certificats. Pour se faire, il faut executer la commande ci-dessous dans une ligne de commande:
```
    keytool -keystore server.keystore.jks -alias sawadogo_kafka_serveur -validity 365 -genkey -keyalg RSA
```
Définir un mot de passe (qu'on utilisera pour l'ensemble des autres fichiers gégénérés) et completer les autres informations. Cette commande va nous générer un fichier server.keystore.jks
Il faut ensuite générer le certificat d'autorité de certification avec la commande ci-dessous et utiliser le meme mot de passe que précédement. Cette commande va nous générer les fichiers ca-cert   ca-key
```
    openssl req -new -x509 -keyout ca-key -out ca-cert -days 365
```
Il faut ensuite créer le fichier de clés certifiées du serveur et importez le certificat de l'autorité de certification. Cela se fait avec la commande ci-dessous qui va nous générer le fichier kafka.server.truststore.jks
```
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file /mnt/c/Users/jmsawadogo/Desktop/usefulRepo/testKafKaZookeper/kafka_2.12-3.2.1/ca-cert
```
Il faut ensuite créez le fichier de clés certifiées du client et importez le certificat de l'autorité de certification. Cela se fait avec la commande ci-dessous. Cette commande va générer le fichier kafka.client.truststore.jks

```
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file /mnt/c/Users/jmsawadogo/Desktop/usefulRepo/testKafKaZookeper/kafka_2.12-3.2.1/ca-cert
```
Il faut ensuite générez un certificat serveur et signez-le à l'aide de l'autorité de certification. Cela se fait avec les commandes ci-dessous qui vont générer les fichiers cert-file et cert-signed :
```
    keytool -keystore server.keystore.jks -alias sawadogo_kafka_serveur -certreq -file cert-file
    openssl x509 -req -CA ca-cert -CAkey ca-key -in cert-file -out cert-signed -days 365 -CAcreateserial
```
Il faut ensuite, importez le certificat de l'autorité de certification dans le magasin de clés du serveur avec la commande ci-dessous:
```
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert
```
Il faut ensuite importez le certificat de serveur signé dans le magasin de clés du serveur avec la commande ci-dessous.
```
    keytool -keystore kafka.server.keystore.jks -alias server.hostname -import -file cert-signed
```
Il faut ensuite exportez le certificat serveur dans le fichier DER binaire. La commande keytool -exportcert utilise le format DER par défaut. Placez le certificat dans le répertoire trusted_certificates/ de tout programme d'émulation qui communique avec Kafka. Vous avez besoin du certificat serveur pour chaque serveur d'amorçage que vous utilisez dans la configuration. Sinon, QRadar rejette l'établissement de liaison TLS avec le serveur.
```
    keytool -exportcert -keystore kafka.server.keystore.jks -alias server.hostname -file server.hostname.der
```
Il faut ensuite générez un magasin de clés client.
```
    keytool -keystore kafka.client.keystore.jks -alias client.hostname -validity 365 -genkey
```
Il faut ensuite générez un certificat client et signez-le à l'aide de l'autorité de certification.
```
    keytool -keystore kafka.client.keystore.jks -alias client.hostname -certreq -file client-cert-file
    openssl x509 -req -CA ca-cert -CAkey ca-key -in client-cert-file -out client-cert-signed -days 365 -CAcreateserial
```
Il faut importez le certificat de l'autorité de certification dans le magasin de clés du client
```
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert
```
Il faut importez le certificat client signé dans le magasin de clés du client.
```
keytool -keystore kafka.client.keystore.jks -alias client.hostname -import -file client-cert-signed
```
#### Installation des clés
Il faut aussi ajouter les lignes ci-dessous au meme fichier kafka_2.12-3.2.1/config/server.properties:
```
    --batch-size=1
    --timeout=0
    delete.topic.enable=true
    security.inter.broker.protocol=SSL
    ssl.client.auth=required
    ssl.keystore.location=/mnt/c/Users/jmsawadogo/Desktop/usefulRepo/kafka_2.12-3.2.1/kafka.server.keystore.jks
    ssl.keystore.password=sawadogo1234
    ssl.key.password=sawadogo1234
    ssl.truststore.location=/mnt/c/Users/jmsawadogo/Desktop/usefulRepo/kafka_2.12-3.2.1/kafka.server.truststore.jks
    ssl.truststore.password=sawadogo1234
    ssl.endpoint.identification.algorithm=
    enable.ssl.certificate.verification=false
    max.poll.records=500
    max.poll.interval.ms=300000

    ssl.enabled.protocols=TLSv1.2,TLSv1.1,TLSv1
    ssl.keymanager.algorithm=SunX509
    ssl.keystore.type=JKS
    ssl.protocol=TLS
    ssl.trustmanager.algorithm=PKIX
    ssl.truststore.type=JKS
    authorizer.class.name=kafka.security.authorizer.AclAuthorizer
    allow.everyone.if.no.acl.found=true
```
## Tester si le https fonctionne
Pour tester si la securisation des communications SSL de Kafka fonctionne, il faut executer la commande ci-dessous:
```
    sudo openssl s_client -debug -connect kafka01.mycompany.com:9093 -tls1
```
Si la sécurisation HTTPS fonctionne, nous aurons une sortie qui ressemblera à quelques choses de ce genre:
```
    CONNECTED(00000003)
write to 0x562e08958ee0 [0x562e0896bcd0] (138 bytes => 138 (0x8A))
0000 - 16 03 01 00 85 01 00 00-81 03 01 75 f4 01 c6 dd   ...........u....
0010 - b5 b9 cc f8 88 3a 7e 23-0d 74 18 14 2e f1 3b 86   .....:~#.t....;.
0020 - 9b 4b f8 27 38 19 ae 91-8f 44 5c 00 00 12 c0 0a   .K.'8....D\.....
0030 - c0 14 c0 09 c0 13 00 35-00 2f 00 39 00 33 00 ff   .......5./.9.3..
0040 - 01 00 00 46 00 00 00 1e-00 1c 00 00 19 73 72 76   ...F.........srv
0050 - 2d 61 70 70 33 36 35 2e-74 69 73 73 65 6f 2d 65   -app365.tisseo-e
0060 - 78 70 2e 64 6f 6d 00 0b-00 04 03 00 01 02 00 0a   xp.dom..........
0070 - 00 0c 00 0a 00 1d 00 17-00 1e 00 19 00 18 00 23   ...............#
0080 - 00 00 00 16 00 00 00 17-00 00                     ..........
read from 0x562e08958ee0 [0x562e08962ab3] (5 bytes => 5 (0x5))
0000 - 15 03 03 00 02                                    .....
read from 0x562e08958ee0 [0x562e08962ab8] (2 bytes => 2 (0x2))
0000 - 02 46                                             .F
140175171114816:error:1409442E:SSL routines:ssl3_read_bytes:tlsv1 alert protocol version:ssl/record/rec_layer_s3.c:1544:SSL alert number 70
---
no peer certificate available
---
No client certificate CA names sent
---
SSL handshake has read 7 bytes and written 138 bytes
Verification: OK
---
New, (NONE), Cipher is (NONE)
Secure Renegotiation IS NOT supported
Compression: NONE
Expansion: NONE
No ALPN negotiated
SSL-Session:
    Protocol  : TLSv1
    Cipher    : 0000
    Session-ID:
    Session-ID-ctx:
    Master-Key:
    PSK identity: None
    PSK identity hint: None
    SRP username: None
    Start Time: 1659601713
    Timeout   : 7200 (sec)
    Verify return code: 0 (ok)
    Extended master secret: no
---
read from 0x562e08958ee0 [0x562e0889cda0] (8192 bytes => 0 (0x0))
```
## Les nouvelles commandes
Une fois le HTTPS et SSL installé, les anciennes commandes ne fonctionneront plus comme précédement. Il faut créer un fichier nommé client-ssl.properties et y mettre les informations suivantes:
```
    security.protocol=SSL
    ssl.truststore.location=/opt/kafka/certs/kafka.client.truststore.jks
    ssl.truststore.password=sawadogo123456
    ssl.keystore.location=/opt/kafka/certs/kafka.client.keystore.jks
    ssl.keystore.password=sawadogo123456
    ssl.key.password=sawadogo123456
    ssl.enabled.protocols=TLSv1.2
    ssl.truststore.type=JKS
    ssl.keystore.type=JKS
    ssl.endpoint.identification.algorithm=
```
les nouvelles commandes s'executerons comme les exemples ci-dessous:
```
    sudo ./bin/kafka-console-consumer.sh --bootstrap-server srv-app365.tisseo-exp.dom:9093 --topic sawadogotestkafka --from-beginning --consumer.config ./client-ssl.properties
    sudo ./bin/kafka-console-producer.sh --broker-list srv-app365.tisseo-exp.dom:9093 --topic sawadogotestkafka --producer.config ./client-ssl.properties
    sudo ./bin/kafka-consumer-groups.sh --list --bootstrap-server srv-app365.tisseo-exp.dom:9093 --command-config ./client-ssl.properties
```

## Création de certificat pour un client
Il faut commencer par chercher l'alias
```
    keytool -list -v -keystore /opt/kafka/certs/kafka.client.keystore.jks
```
Pour générer le certificate.pem
```
sudo keytool -exportcert -alias srv-app365.tisseo-exp.dom -keystore /opt/kafka/certs/kafka.client.keystore.jks -file /opt/kafka/certs/certificate.pem -storepass sawadogo1900
```

Pour générer la clé
```
sudo keytool -v -importkeystore -srckeystore /opt/kafka/certs/kafka.client.keystore.jks -srcalias srv-app365.tisseo-exp.dom -destkeystore /opt/kafka/certs/cert_and_key.p12 -deststoretype PKCS12 -storepass sawadogo1900 -srcstorepass sawadogo1900
sudo openssl pkcs12 -in /opt/kafka/certs/cert_and_key.p12 -nodes -nocerts -out /opt/kafka/certs/key.pem -passin pass:sawadogo1900
```

Pour générer le caroot
```
sudo keytool -exportcert -alias srv-app365.tisseo-exp.dom -keystore /opt/kafka/certs/kafka.client.keystore.jks -rfc 
-file /opt/kafka/certs/CARoot.pem -storepass sawadogo1900
```

## Ajouter un nouveau noeud à notre cluster
Pour ajouter un nouveau nœud à notre cluster, il suffit de télécharger la même version de Kafka qui a été téléchargé et de le dézipper dans la machine de destination. Cela se fait avec les commandes ci-dessous dans notre cas :
```
    wget https://dlcdn.apache.org/kafka/3.2.1/kafka_2.12-3.2.1.tgz
    tar xzf kafka_2.12-3.2.1.tgz
```
Il faut ensuite modifier la configuration du nouveau nœud de socket.request.max.bytes en le faisant passer à 369296128. Pour cela décommenter et modifier la valeur de socket.request.max.bytes comme ici:
```
    socket.request.max.bytes=369296128
```
Il faut ensuite ajouter les deux valeurs ci-dessous à la configuration:
```
    max.poll.records=500
    max.poll.interval.ms=300000
```

# Installation de Kafka Manager


#### Remarque : [Documentation Kafka](https://kafka.apache.org/documentation/)

#### Création et Activation du systemd
Pour activé le systemd, il faut créer des fichiers unitaires systemd pour les services Zookeeper et Kafka. Ce qui vous aidera à démarrer/arrêter le service Kafka de manière simple.
Il faut créer le fichier zookeeper.service dans system avec la commande ci-dessous:
```
    sudo nano /etc/systemd/system/zookeeper.service
```
Il faut ensuite remplir ce fichier avec les informations ci-dessous:
```
    [Unité]
    Description=Serveur Apache Zookeeper
    Documentation=http://zookeeper.apache.org
    Requiert=network.target remote-fs.target
    After=network.target remote-fs.target

    [Service]
    Genre=simple
    ExecStart=/usr/local/kafka/bin/zookeeper-server-start.sh /usr/local/kafka/config/zookeeper.properties
    ExecStop=/usr/local/kafka/bin/zookeeper-server-stop.sh
    Redémarrer=sur-anormal

    [Installer]
    WantedBy=multi-utilisateur.cible
```
Il faut ensuite, créer le fichier kafka.service avec la commande ci-dessous:
```
    sudo nano /etc/systemd/system/kafka.service
```
Il faut ensuite remplir ce fichier avec les informations ci-dessous:
```
    [Unit]
    Description=Apache Kafka Server
    Documentation=http://kafka.apache.org/documentation.html
    Requires=zookeeper.service

    [Service]
    Type=simple
    Environment="JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64"
    ExecStart=/usr/local/kafka/bin/kafka-server-start.sh /usr/local/kafka/config/server.properties
    ExecStop=/usr/local/kafka/bin/kafka-server-stop.sh

    [Install]
    WantedBy=multi-user.target
```
Il faut ensuite executer les commandes ci-dessous:
```
    systemctl daemon-reload
``` 
```
    sudo systemctl start zookeeper
```
```
    sudo systemctl start kafka
```
```
    sudo systemctl status kafka
```
