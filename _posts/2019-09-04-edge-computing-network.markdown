---
layout: post
title:  "Thinking on Edge Computing Networking"
categories: jekyll update
---

Networking is a very important aspect when considering on Edge Computing, How the edge devices are connected? How the edge devices are connected to centric cloud.

Kubernetes is the most popular architecture used in Edge Computing field, this post takes this architecture as example to illustrate networking in Edge Computing.

![architecture](/images/flannel/overview.png)

Pod is one of resource in Kubernetes, it is a group of containers which share network namespace.

# Connection between pods

How about the connection between pods, to illustrate it, here setup an environment for validation. 
In this example, Flannel is selected as networking plugin, all pods are in address range 10.217.0.0/16.  
The setup progress of this test can be found on Appendix A.

![architecture](/images/flannel/flannel.png)

The upper part is just like the centric cloud, where Kubernetes Master, and centric nodes located.

The lower part is just like the edge devices group.

In such connections, the edge devices are hidden behind the router in NAT, the edge devices can access centric nodes directly, but not vise verse.

Below list the test result of connections from pod1.a to other pods and internet.

`pod1.a`, ip is 10.217.4.3, in edge device worker1 (192.168.22.53), from pod1.a to
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
  
# Connect through services

Follow previous example, add services to each pods and check the connections between them again.

![architecture](/images/flannel/service.png)

Connection to service is through iptables, not through vxlan tunnel, so it is a little different.

Take `service1` as example, from service1 to
* service2  
  `Reachable`
* servicex  
  `Reachable`

See Appendix B for the setup progress.


# Thinking on Edge Computing Networking 
Refer to previous example, we can see,
* Pods in edge devices can access each other only when their hosts can access each other directly, normally in the same region.

* Pods in edge devices can not access pods in centric nodes in tunnel mode, the option way is through public ip, or kubernetes service.

* Access to services is not through tunnel, it's more like the normal way. Only services associated with pods which host has public ip are available.


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
### B. steps to setup services example
```
richard@kubernetes-master:~/k8s/books$ cat services-test.yaml 
---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: dpy-worker1
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: pod-worker1
    spec:
        nodeSelector:
          kubernetes.io/hostname: kubernetes-worker1
        containers:
          - name: cnt-worker1
            image: jiangjqian/edgegw
            ports:
              - containerPort: 80

---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: dpy-worker2
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: pod-worker2
    spec:
        nodeSelector:
          kubernetes.io/hostname: kubernetes-worker2
        containers:
          - name: cnt-worker2
            image: jiangjqian/edgegw
            ports:
              - containerPort: 80


---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: dpy-node1
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: pod-node1
    spec:
        nodeSelector:
          kubernetes.io/hostname: node1
        containers:
          - name: cnt-worker1
            image: jiangjqian/edgegw
            ports:
              - containerPort: 80



---                                          
apiVersion: v1
kind: Service
metadata:
        name: service-worker1
spec:
        selector:
                app: pod-worker1
        ports:
              - protocol: TCP
                port: 1701
                targetPort: 80


---                                          
apiVersion: v1
kind: Service
metadata:
        name: service-worker2
spec:
        selector:
                app: pod-worker2
        ports:
              - protocol: TCP
                port: 1702
                targetPort: 80


---                                          
apiVersion: v1
kind: Service
metadata:
        name: service-node1
spec:
        selector:
                app: pod-node1
        ports:
              - protocol: TCP
                port: 1801
                targetPort: 80


```



