This repository is for my ansible files.

The kubernetes folder contains the files for preparing server to install kubernetes cluster and join to the cluster.

Please use the following scripts :

```
 sudo ansible servers -m ping -i inventory.ini -u root
 
 sudo ansible-playbook -i inventory.ini Kubernetes/ServerPrepare.yml -u root
```
