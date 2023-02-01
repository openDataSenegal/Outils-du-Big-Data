# Introduction
Ce tutoriel explique l'installation d'un cluster HDFS (Hadoop Distributed File System) sur des machines équipées d'un système d'exploitation UBUNTU 20.04. Dans ce tutoriel, on expliquera l'installation de la version 3.3.4 de HDFS qui est la version stable actuelle.

# Mises à jour et Installation de Java
La première chose à faire et la mise à jour de notre système d'exploitation Ubuntu et l'installation ou la mise à jour de Java si cela n'est pas déjà fait sur notre système d'exploitation. Pour cela, il faut executer les commandes ci-dessous:
```
  sudo apt update 
```
```
  sudo apt install openjdk-8-jdk -y
```
```
  java -version; javac -version
```

# Définition des variables d'environnement
Il faut ensuite définir les variables d'environement dans le fichier .bashrc comme suite :
```
  export HADOOP_HOME=/home/hdoop/hadoop-3.3.4
  export HADOOP_INSTALL=$HADOOP_HOME
  export HADOOP_MAPRED_HOME=$HADOOP_HOME
  export HADOOP_COMMON_HOME=$HADOOP_HOME
  export HADOOP_HDFS_HOME=$HADOOP_HOME
  export YARN_HOME=$HADOOP_HOME
  export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
  export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin
  export HADOOP_OPTS"-Djava.library.path=$HADOOP_HOME/lib/nativ"
```
Remplacez /home/hdoop/hadoop-3.3.4 par le chemin menant au fichier hadoop décompressé.
Actualisez la mise à jour du fichier .bashrc avec la commande :
```
  source ~/.bashrc
```

# Téléchargement et configuration de HDFS
Il faut ensuite télécharger et décompresser les binaires de HDFS avec les commandes ci-dessous:
```
  wget https://downloads.apache.org/hadoop/common/hadoop-3.3.4/hadoop-3.3.4.tar.gz
  tar xzf hadoop-3.3.4.tar.gz
 ```
Il faut ensuite modifier le fichier hadoop-env.sh de HDFS en utilisant la commande ci-dessous qui utilise la variable d'environement HADOOP_HOME qui definie le chemin vers hadoop 
```
  sudo nano $HADOOP_HOME/etc/hadoop/hadoop-env.sh
```
ajouter dans ce fichier la définition de la variable d'envirronement JAVA_HOME
```
  export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
```
Pour trouver ce chelin, executer les commandes ci-dessous :
```
    which javac
    readlink -f /usr/bin/javac
```
ou /usr/bin/javac est le resultat donné par la commande which javac.

Modifier ensuite le fichier core-site.xml en utilisant la commande ci-dessous:
```
  sudo nano $HADOOP_HOME/etc/hadoop/core-site.xml
```
Ajouter dans ce fichier les configurations ci-dessous : 

```
  <property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost:9000</value>
  </property>

```
Ajouter les configurations ci-dessous aux fichier hdfs-site.xml en utilisant la commande ci-dessous:
```
  <property>
    <name>dfs.data.dir</name>
    <value>/data-hdfs/dfsdata/namenode</value>
  </property>
  <property>
    <name>dfs.data.dir</name>
    <value>/data-hdfs/dfsdata/datanode</value>
  </property>
  <property>
    <name>dfs.namenode.name.dir</name>
    <value>/data-hdfs/dfsdata/namenode</value>
  </property>
  <property>
    <name>dfs.datanode.data.dir</name>
    <value>/data-hdfs/dfsdata/datanode</value>
  </property>
  <property>
    <name>dfs.replication</name>
    <value>2</value>
  </property>
```
Ou le repertoire /data-hdfs/dfsdata/ a été créé par nous et est destiné à contenir dans ces sous-repertoire datanode et namenode les données des datanodes et des namenodes
dfs.replication Spécifie le nombre de réplicat qu'on veut réaliser
Après cela, il faut formater les repertoires avec le commande suivante:
```
  ./bin/hdfs namenode -format
```
Une fois ces opérations réalisées, notre cluster est pret a etre lancer, pour cela executer les commandes ci-dessous pour lancer le datanode et le namenode dans deux terminales différents
```
  ./bin/hdfs namenode
  ./bin/hdfs datnode
```
On peut ensuite vérifier ci le cluster fonctionne très bien en executant la commande 
```
  ./bin/hdfs dfs -ls /
```
# Connecter HDFS avec python en utilisant l'API REST
HDFS dispose d'une API qui permet de réaliser des opérations dessus. On peut utiliser la librairie python hdfs pour se connecter au cluster grace à l'API
```
  import hdfs
  client = hdfs.InsecureClient("http://adress_ip:9870")

  # Affichage des données à la racine
  # l'appel à "list()" renvoie une liste de noms de fichiers et de
  # répertoires
  client.list("/")
```
# Ajouter un nouveau Noeuds

to do

# Configurer le HTTPS
Pour configurer la connexion HTTPS de hadoop, il faut créer un repertoire certifs dans le repertoire de hadoop (hadoop-3.3.4) et se placer dessus et suivre les étapes suivantes:
 -  Il faut generer le keystore pour chaque noeuds avec la commande ci-dessous:
   ```
      keytool -genkey -keyalg RSA -alias c6401 -keystore keystore.jks -storepass bigdata -validity 360 -keysize 2048
   ```
  Cette commande va générer un fichier keystore.jks dans le repertoire certifs.
  - Il faut ensuite générer le CRS du keystore avec la commande ci-dessus : 
   ```
      keytool -certreq -alias c6401 -keyalg RSA -file c6401.csr -keystore keystore.jks -storepass bigdata
   ```
   Cette commande va générer un fichier c6401.csr dans le repertoire certifs
  - Créer le fichier cert à partir du CA avec la commande ci-dessous: 
   ```
      keytool -import -alias root -file ca.crt -keystore keystore.jks
   ```
   
  
# Configurer l'authentification

to do



