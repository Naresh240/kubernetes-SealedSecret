# kubernetes-SealedSecret

## Pre-Requisites

```bash
kubernetes cluster
```

## kubernetes cluster - minikube
[minikube setup](https://github.com/Naresh240/kubernetes/blob/main/minikube-setup/README.md)

## Install SealedSecret

```bash
echo "**** Installing kubeseal ****"

export VERSION_KUBESEAL=0.17.5
curl -LO https://github.com/bitnami-labs/sealed-secrets/releases/download/v${VERSION_KUBESEAL}/kubeseal-${VERSION_KUBESEAL}-linux-amd64.tar.gz
tar -xzvf kubeseal-${VERSION_KUBESEAL}-linux-amd64.tar.gz
chmod +x kubeseal
 mv kubeseal /usr/bin/
```

## Deploy the sealed-secret controller

```bash
wget https://github.com/bitnami-labs/sealed-secrets/releases/download/v0.18.0/controller.yaml
kubectl apply -f controller.yaml
```

You can check the status of your deployment by using the following command

```bash
kubectl get pods -n kube-system | grep sealed-secrets-controller
```

## Create Secret file as shown below in local

```bash
---
apiVersion: v1
kind: Secret
metadata:
  name: <name-of-secret>
data:
  user: <base64encode>
  password: <base64encode>
```  

## Get master key (means certificate to seal a secret) from sealedsecret

```bash
kubeseal --fetch-cert > master-key.pem  
```

## Seal Secret with master key

```bash
kubeseal --cert=master-key.pem  --format=yaml < <secret-file>.yaml > sealed-secret.yaml
```

## Unseal secret under cluster where we are using

```bash
kubectl apply -f sealed-secret.yaml
```

## Deploy mysql with sealed secret

## Create sealed secret for secrets.yaml (This file not required to push to Github)

```bash
kubeseal --fetch-cert > master.pem 
kubeseal --cert=master.pem --format=yaml < secrets.yml > sealed-secret-mysql.yaml
```

## Deploying mysql in kubernetes cluster

```bash
kubectl apply -f pv.yml
kubectl apply -f pvc.yml
kubectl apply -f configmaps.yml
kubectl apply -f sealed-secret-mysql.yaml         # This secret will be unseal using controller and master.pem file
kubectl apply -f deployment.yml
```
```Note:``` Push sealed secret file to Github and share master.pem personally who required to unseal, Here I have given master key for understanding

## Connect to mysql pod

```bash
kubectl exec -it <pod-name> -- /bin/bash
```

## Connect to mysql

```bash
mysql -u naresh -p
Provide password for mysql
```

## Connect to mysqldb

```bash
 use mysqldb
```

## Create "employee" table in mysqldb

```bash
create table employee( empno varchar(40), ename varchar(40));
```

## Insert data into "employee" table

```bash
insert into employee(empno, ename) values("101","Naresh");
```

## Check table data

```bash
select * from employee;
```
