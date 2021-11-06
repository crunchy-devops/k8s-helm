# Lab-helm

## Install  NFS on master  
```shell
# deploy first
sudo apt-get update   # update package
sudo apt-get install -y nfs-kernel-server  # install nfs packages
sudo mkdir /opt/mysql  # create shared directory
sudo chmod 1777 /opt/mysql   # change to executable mode

#Add a line in /etc/exports  
sudo vi /etc/exports
/opt/mysql/  *(rw,sync,no_root_squash,subtree_check)
sudo exportfs -ra   # reload for the change takes effect
```

## Install nfs on the node1
```shell
sudo apt-get update 
sudo apt-get -y install nfs-common  # add  nfs client package
showmount -e <master_address_ip>   # check whether the shared mount works

## Installer Helm

### Installation de binaire helm
```shell script
wget https://github.com/helm/helm/archive/refs/tags/v3.6.3.tar.gz
tar -zxvf v3.6.3.tar.gz
cd helm-3.6.3/
make
cd bin
sudo cp  helm /usr/local/bin/helm
helm version
``` 

### Add  bitnami repo to helm 
```shell script
    helm repo add bitnami https://charts.bitnami.com/bitnami
    helm repo update
    k create -f pvol.yaml
    k get pv
    k create -f pvolclaim.yaml
    k get pvc
    helm install  mysql --set mysqlRootPassword=rootpassword,mysqlUser=mysql,mysqlPassword=my-password,mysqlDatabase=mydatabase,persistence.existingClaim=mysql-pvc stable/mysql
    k get pod
    k get pod -w #  k is an alias so it can't be used in a command shell using watch
    k get pod --watch 
```
## Get mysql admin password
```shell script
   MYSQL_ROOT_PASSWORD=$(kubectl get secret --namespace default mysql -o jsonpath="{.data.mysql-root-password}" | base64 --decode; echo)
   echo $MYSQL_ROOT_PASSWORD
```
## se connecter a Mysql en dehors du cluster 
```shell script
     sudo apt-get install -y mysql-client-core-5.7
     k port-forward svc/mysql 3306
     # in another shell 
     mysql -h 127.0.0.1 -P 3306 -u root -p
``` 

# Troubleshooting if helm failed 
alias helm='docker run -e KUBECONFIG="/root/.kube/config:/root/.kube/some-other-context.yaml" -ti --rm -v $(pwd):/apps -v ~/.kube:/root/.kube -v ~/.helm:/root/.helm alpine/helm'