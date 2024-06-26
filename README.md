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

The vms hosted on virtualbox are like the below schema :

![alt text](https://raw.githubusercontent.com/kayvansol/Ingress/main/pics/vmnet.png?raw=true)

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
sudo kubeadm init  --control-plane-endpoint="192.168.56.118:6443"   
      --upload-certs  --apiserver-advertise-address=192.168.56.120
      --pod-network-cidr=192.168.0.0/16  
      --cri-socket=unix:///var/run/cri-dockerd.sock  
      --ignore-preflight-errors=all 
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

and run the below on other servers to join to the cluster :

On control-plane (e.g. 192.168.56.122) :

```
kubeadm join 192.168.56.118:6443 --token c4c6wt.2rzubblajmxx7wf1 \
     --discovery-token-ca-cert-hash sha256:91877d933445148c650e5fa11acca05d455fe1e9e53cd33f8497ad06a2126142 \
     --control-plane --certificate-key 2e8c3d0a1f2d4aec3e4ccb09a0dd6f43756344269c0b414cdd83c0ef02c0293d \
     --apiserver-advertise-address=192.168.56.122 
     --cri-socket=unix:///var/run/cri-dockerd.sock 
     --ignore-preflight-errors=all
```
On worker nodes :
```
kubeadm join 192.168.56.118:6443 --token  c4c6wt.2rzubblajmxx7wf1 \
     --discovery-token-ca-cert-hash sha256:91877d933445148c650e5fa11acca05d455fe1e9e53cd33f8497ad06a2126142 \
     --cri-socket=unix:///var/run/cri-dockerd.sock 
     --ignore-preflight-errors=all
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
