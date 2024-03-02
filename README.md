This repository is for my ansible files.

The kubernetes folder contains the files for preparing server to install kubernetes cluster or join to the cluster.

Please run the following script on all servers :

```
 sudo ansible servers -m ping -i inventory.ini -u root
 
 sudo ansible-playbook -i inventory.ini Kubernetes/ServerPrepare.yml -u root
```

Kubernetes Cluster :

HAProxy server (Load Balancer) address is : 192.168.56.118 
    (frontend bind to 192.168.56.118:6443)
    (backend  bind to 192.168.56.126:6443)

For starting a Kubernetes cluster, follow the below lines :

Run below scripts only on 192.168.56.126 :

```
sudo kubeadm init --control-plane-endpoint="192.168.56.118:6443"    --upload-certs               --apiserver-advertise-address=192.168.56.126    --pod-network-cidr=192.168.0.0/16   --cri-socket=unix:///var/run/cri-dockerd.sock    --ignore-preflight-errors=all  
```

and run the below on other servers (other control-plane nodes or worker nodes) to join to the cluster :

```
kubeadm join 192.168.56.118:6443 --token ...
```

And below code for all nodes :

```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
