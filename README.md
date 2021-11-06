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
# add the following line
/opt/mysql/  *(rw,sync,no_root_squash,subtree_check) # save the file containing the line 
sudo exportfs -ra   # reload for the change takes effect
```

## Install nfs on the node1
```shell
sudo apt-get update 
sudo apt-get -y install nfs-common  # add  nfs client package
showmount -e <master_address_ip>   # check whether the shared mount works

## Installer Helm

### Install using helm source code
```shell script
wget https://github.com/helm/helm/archive/refs/tags/v3.6.3.tar.gz  # get all source code
tar -zxvf v3.6.3.tar.gz  # untar 
cd helm-3.6.3/  # down to the relevant directory
make  # build helm binary 
cd bin # get the result
sudo cp  helm /usr/local/bin/helm # move to a system directory
helm version # check 
``` 

### Add  bitnami repo to helm 
```shell script
    helm repo add bitnami https://charts.bitnami.com/bitnami  # assign a repo to helm 
    helm repo update  # check for update
    helm search repo bitnami  # list oll chart in bitnami helm repo
    k create -f pvol.yaml  # Create a persistent volume using nfs
    k get pv  # check
    k create -f pvolclaim.yaml # Create a persistent volume claim 
    k get pvc # check
    k get pv  # check the bindings
    helm install mysql-bitnami bitnami/mysql --set auth.rootPassword=password --set primary.persistence.existingClaim=pvc-three
    k get pod
    k get pod -w #  k is an alias so it can't be used in a command shell using watch
    k get pod --watch 
```

## Check 
```shell script
     sudo apt-get install -y mysql-client-core-5.7 # install mysql client
     k port-forward svc/mysql-bitnami 3306  # port forward the mysql port
     # open an other shell 
     mysql -h 127.0.0.1 -P 3306 -u root -p # check, you should get mysql database prompt
``` 

