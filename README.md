![alt text](https://raw.githubusercontent.com/kayvansol/Ingress/main/pics/kubernetes.gif?raw=true)

This repository is for deploying kubernetes on-permises cluster (bare-metal servers). 

The kubernetes folder contains the files for preparing server to install kubernetes cluster or join to the cluster.

Please run the following script on all servers :

```
 sudo ansible servers -m ping -i inventory.ini -u root
 
 sudo ansible-playbook -i inventory.ini Kubernetes/ServerPrepare.yml -u root
```

Kubernetes Cluster :

The control-plane nodes addresses are :

    192.168.56.120
    192.168.56.121
    192.168.56.122

The worker nodes addresses are :

    192.168.56.123
    192.168.56.124

HAProxy server (Load Balancer for kube apiserver) address is : 
```
192.168.56.118 
```

haproxy.cfg :

    stats enable
    (frontend bind to 192.168.56.118:6443)
    (backend  bind to 192.168.56.120:6443  192.168.56.121:6443  192.168.56.122:6443)

For starting a Kubernetes cluster, follow the below lines :

Run below scripts only on 192.168.56.120 :

```
sudo kubeadm init --control-plane-endpoint="192.168.56.118:6443"  --upload-certs  --apiserver-advertise-address=192.168.56.120
   --pod-network-cidr=192.168.0.0/16   --cri-socket=unix:///var/run/cri-dockerd.sock    --ignore-preflight-errors=all  
```

And below code for all nodes :

```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

Install Calico network policy for on-premises deployments, 50 nodes or less :
```
curl https://raw.githubusercontent.com/projectcalico/calico/v3.27.2/manifests/calico.yaml -O

kubectl apply -f calico.yaml
```

and run the below on other servers (other control-plane or worker nodes) to join to the cluster :

```
kubeadm join 192.168.56.118:6443 --token ...
```

and at final step enjoy from your cluster :

```
kubectl get nodes -o wide

kubectl get pod -A
```
![alt text](https://github.com/kayvansol/Ansibles/blob/main/Images/1.jpeg?raw=true)

![alt text](https://github.com/kayvansol/Ansibles/blob/main/Images/2.jpeg?raw=true)

HAProxy Stats :

![alt text](https://github.com/kayvansol/Ansibles/blob/main/Images/3.jpeg?raw=true)

haproxy.cfg :

![alt text](https://github.com/kayvansol/Ansibles/blob/main/Images/4.jpeg?raw=true)
