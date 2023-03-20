# Introduction
Ce tutoriel explique l'installation d'un cluster Kubernete sur des machines équipés d'un système d'exploitation UBUNTU 20.04.
Dans ce tutoriel, on expliquera l'installation de la dernière version de Kubernetes. On se limitera à l'installation d'un cluster Kubernetes composé de deux nœuds.

# Installation des outils Annexes
On commence par mettre à jours le systeme ubuntu avec les commandes suivantes:
 ```
      sudo apt update
      sudo apt upgrade
 ```
Il faut ensuite installer docker s'il n'est pas deja installé dans le système. Pour cela, on utilise les commandes suivantes :

      ```
         sudo apt install docker.io
         docker ––version
      ```

Et enfin on installe les outils annexes :
   -  Installation de conntrack
      ```
         sudo apt -y install conntrack
      ```
   -  Installation crictl 
      ```
         VERSION="v1.12.0" && \ 
         wget https://github.com/kubernetes-sigs/cri-tools/releases/download/$VERSION/crictl-$VERSION-linux-amd64.tar.gz && \
         sudo tar zxvf crictl-$VERSION-linux-amd64.tar.gz -C /usr/local/bin && \
         rm -f crictl-$VERSION-linux-amd64.tar.gz
      ```

# Installation de Kubernetes

On installe ensuite Kubernetes avec les commandes ci-dessous :
      ```
         curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add
         sudo apt-add-repository "deb http://apt.kubernetes.io/kubernetes-xenial main"
      ```
Il faut ensuite supprimer le fichier /etc/apt/sources.list.d/archive_uri-http_apt_kubernetes_io_kubernetes-xenial-jammy.list si on rencontre des erreurs. Pour cela, utiliser la commande ci-dessous:
      ```
         rm /etc/apt/sources.list.d/archive_uri-http_apt_kubernetes_io_kubernetes-xenial-jammy.list
      ```
L'installation des outils kubeadm kubelet kubectl avec la commande ci-dessous : 
      ```
         sudo apt-get install kubeadm kubelet kubectl
         sudo apt-mark hold kubeadm kubelet kubectl
      ```
On peut verifier si l'installation de kubeadm s'est bien passé en regardant la version avec la commande ci-dessous :
      ```
         kubeadm version 
      ```

# Parametrage de Kubernetes

Une fois l'installation de Kubernetes reussi, il faudra le parametrer. Pour cela, il faut commencer par desactivé la memoire swap avec la commande ci-dessous :

      ```
         swapoff –a
      ```
      
Ensuite, assigner un nom de hote aux noeuds avec les commandes ci-dessous:

      ```
         sudo hostnamectl set-hostname master-node
         sudo hostnamectl set-hostname w1
      ```
      
Configuration du noeuds
      ```
         sudo kubeadm init --pod-network-cidr=10.244.0.0/16
         mkdir -p $HOME/.kube
         sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
         sudo chown $(id -u):$(id -g) $HOME/.kube/config         
      ```
























-  Installation de curl
      ```
         sudo apt install apt-transport-https curl
         curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add
         echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" >> ~/kubernetes.list
         sudo mv ~/kubernetes.list /etc/apt/sources.list.d
      ```
Installation des outils necessaire pour installation de kubernetes
```
    sudo apt update
    sudo apt install kubelet
    sudo apt install kubeadm
    sudo apt install kubectl
    sudo apt-get install -y kubernetes-cni
```
Si ces commandes ne fonctionnent pas, utiliser sudo snap install ...

Desactivé la memoire swap et les nom de domaine
```
    sudo swapoff -a
    sudo nano /etc/fstab
    sudo hostnamectl set-hostname kubernetes-master
    sudo hostnamectl set-hostname kubernetes-worker
```


Configuration
 ```
    sudo kubeadm init --ignore-preflight-errors=NumCPU,Mem --pod-network-cidr=10.244.0.0/16
    mkdir -p $HOME/.kube
    sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
    sudo chown $(id -u):$(id -g) $HOME/.kube/config
 ```
 ```
    sudo ufw allow 6443/tcp
 ```
 
 Commandes utiles
 ```
    kubectl get pods
    kubectl get service
    kubectl version --client -o json
    kubectl get pods --all-namespaces
    kubectl get namespaces
    kubectl get svc
    kubectl config view
    kubectl config get-contexts
    kubectl get pods -o wide
    kubectl events --types=Warning
 ```
 
 
 
 
 
 
 
 
 
 
 
 
