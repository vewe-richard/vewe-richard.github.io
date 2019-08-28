---
layout: post
title:  "Edge Computing based on Kubernetes, Demo Series 1 - VPN service"
categories: jekyll update
---

# Overview
From this post, I am going to present a series of demos on Edge Gateway based on Kubernetes Architecture.
![architecture](/images/egw-architecture.png)

  * On the right part, group of servers provide Iaas (through openstack for example), Kubernetes master and various centric service run on these Vms.

  * On the left part, Edge devices (it can be the eNB, CPE or router) provides edge computing service.

# The VPN Demo

On this demo, it presents to deploy a VPN service from Kubernetes Master, it deploys a pptp container to the edge gateway. See the architecture.

![demo-architecture](/images/pptp/demo-architecture.png)

On the upper part, 
  * The right part, it runs the Kubernetes master, and the dashboard for user to deploy services.

  * The left part, the edge gateway is a Kubernetes node, after deployed of VPN service, users can access enterprise network behind the edge gateway.

# Hardware Requirements

  * One PC as the Kubernetes master

  * One NXP LS1043A box as the Kubernetes node 

# Install of Kubernetes

  Follow [Setup NXP LS1043A as a Kubernetes Node in Flannel networking](/jekyll/update/2019/08/21/setup-nxp1043-as-k8s-node.html)

# Deploy VPN service

  * Prepare docker image for VPN server

This Demo use PPTP to setup VPN server, dockerfiles used to build the docker image.
```
FROM ubuntu
RUN apt-get update && \
    apt-get install -y pptpd iptables && \
    rm -rf /var/lib/apt/lists/*

RUN echo "username * password *" >> /etc/ppp/chap-secrets && \
    echo "localip 192.168.10.1" >> /etc/pptpd.conf && \
    echo "remoteip 192.168.10.100-200" >> /etc/pptpd.conf && \
    echo "ms-dns 8.8.8.8" >> /etc/ppp/pptpd-options

ADD run.sh /run.sh

CMD ["/run.sh"]
```
see [dockerfiles on github](https://github.com/vewe-richard/dockerfiles/tree/master/pptpd)
  
  
  
  * Prepare Service describe file for Kubernetes

```
richard@kubernetes-master:~/k8s/books$ cat service-pptpd.yaml
---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
        name: pptpd-dpy-amd64
spec:
        replicas: 1
        template:
                metadata:
                        labels:
                                app: pptpd-pod
                spec:
                        nodeSelector:
                                beta.kubernetes.io/arch: amd64
                        hostNetwork: true
                        containers:
                                - name: pptpd-cnt-amd64
                                  image: jiangjqian/pptpd
                                  securityContext:
                                        privileged: true
---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
        name: pptpd-dpy-arm64
spec:
        replicas: 1
        template:
                metadata:
                        labels:
                                app: pptpd-pod
                spec:
                        nodeSelector:
                                beta.kubernetes.io/arch: arm64
                        hostNetwork: true
                        containers:
                                - name: pptpd-cnt-arm64
                                  image: jiangjqian/pptpd-arm
                                  securityContext:
                                        privileged: true
---                                          
apiVersion: v1
kind: Service
metadata:
        name: pptpd-service
spec:
        selector:
                app: pptpd-pod
        ports:
              - protocol: TCP
                port: 1723
```
  
  
  
  * Deploy through dashboard

![deploy](/images/pptp/deploy.png)
   
   
   
  * Check the Service from Kubernetes

![result](/images/pptp/results.png)  
   
   
   
  * Connect from pptp client  

    * Username: username
    * Password: password
    * Security and Compression: MPPE






