---
layout: post
title:  "Edge Computing Networking Consideration"
categories: jekyll update
---

Networking is a very important aspect on considering Edge Computing, How the edge devices are connected? How the edge devices are connected to centric cloud.

Kubernetes is the most popular architecture used in Edge Computing field, this post talks about the networking of Edge Computing in such architecture.

Before dive into discussion, Let's first look at an example to demonstrate the networking simulating edge computing.

# An Example

This example simulates connections on edge computing environment. 

The upper part is just like the centric cloud, where Kubernetes Master, and centric nodes located.

The lower part is just like the edge devices group.

In such connections, the edge devices are hidden behind the router in NAT, the edge devices can access centric nodes directly, but not vise verse.

In this example, the networking plugin is Flannel, all pods are in address range 10.217.0.0/16.

![architecture](/images/flannel/flannel.png)

Below list the test result of connections from pod1.a to other pods and internet.

pod1.a, ip is 10.217.4.3, in edge device worker1 (192.168.22.53), from pod1.a to
* to pod2.a (10.217.3.18) in worker2 (192.168.22.54)  
  `Reachable`  
  pod1.a is connected to pod2.a through vxlan tunnel  
  Note: check if FORWARD is accept if failed to connect.  
  ```iptables -P FORWARD accept```

* to pody (10.217.1.3) in centric node1 (172.16.1.143)  
  `Unreachable`  
  As node1 can not access worker1 directly, the vxlan tunnel can not be setup.  
  In this case, only set the route to worker1 in node1 can make the connection.

* to internet, for example 8.8.8.8  
  `Reachable`   
  Need set the host to NAT  
  `iptables -t nat -A POSTROUTING -o enp0s3 -j MASQUERADE`
  

# Thinking on Edge Computing Networking 
Refer to previous example, we can see,
* Pods in edge devices can access each other only when their hosts can access each other directly, normally in the same region.

* Pods in edge devices can not access pods in centric nodes in normal way, to access pods in centric nodes, the option way is through public ip, and port mapping between pods and their host.


# Appendix
### A. steps to set previous example
```
# Prepare 5 hosts or vms, two as the edge devices, three in centric region, for example
# In this example, all hosts are ubuntu 18.04
# 192.168.22.0/24
richard@richard-server:~/vewe-richard.github.io$ vboxmanage list runningvms
"ubt1804_k8s_node" {e25b6807-ca72-43b2-8ab7-0ce29b4b2e26}
"ubt1804_k8s_node2" {75e4c3a2-f60e-4f38-9089-68a1c4d6ad56}

# 172.16.1.0/24
richard@richard-RC410-RC510-RC710:~$ vboxmanage list runningvms
"k8s_master" {f2537f6b-057c-4541-b0ac-2b033849b651}
"k8s_node1" {ad7f9dba-a181-4c81-957c-56ae085a339d}
"k8s_node2" {05895278-91bf-4f20-8499-fb52edf6b477}


# Prepare
sudo kubeadm reset

# set /etc/cloud/cloud.cfg not to revert back the hostname
preserve_hostname: true

# Change Hostname
sudo hostnamectl set-hostname kubernetes-master

# Master
sudo kubeadm init --pod-network-cidr=10.217.0.0/16 -v8

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

kubectl create -f kube-flannel.yml

# wait for all pods ready
kubectl get pods -o wide --all-namespaces

# Slaves
kubeadm join 172.16.1.233:6443 --token 0z0sj9.99vlr8q0lvqrrguq \
    --discovery-token-ca-cert-hash sha256:985fa33755a27d64d09802c1c8d66529013c22609e97d7d40ffe5810dce380ca

--------------------------------------------------------------------------------------------------------------

richard@kubernetes-master:~/k8s/books$ kubectl apply -f deployment-test.yaml 
deployment.extensions/dpy-test created
richard@kubernetes-master:~/k8s/books$ cat deployment-test.yaml 
---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: dpy-test
spec:
  replicas: 4
  template:
    metadata:
      labels:
        app: podtest
    spec:
        containers:
          - name: cnttest
            image: jiangjqian/edgegw
            ports:
              - containerPort: 80

```
