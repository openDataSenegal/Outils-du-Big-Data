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
to do














