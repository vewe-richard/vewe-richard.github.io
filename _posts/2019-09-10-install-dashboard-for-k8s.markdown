---
layout: post
title:  "Install Dashboard for Kubernetes"
categories: jekyll update
---

There are many guides on installing dashboard for kubernetes. But, it's very easy falling into some traps. Target of this post is to point out those traps that you may fall.

# Installation Guide

This is the guide, [Kubernetes Web UI(Dashboard) Guide](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)

Follow previous guide, If you don't get any problem, Congratulation!  

Else, just continue.  

# A simple example

For easy of description, We take a simple environment as example, a simple kubernetes environment with only one master and one node.

![overview](/images/dashboard/dashboard.png)

Steps:
#### 1. Setup the host to run kubectl (192.168.22.55 in this example)
```
#cp kube config from master to this host
mkdir -p $HOME/.kube
scp root@172.16.1.246:/etc/kubernetes/admin.conf $HOME/.kube/config
chown $(id -u):$(id -g) $HOME/.kube/config
```

#### 2. apply dashboard (in 192.168.22.55)
```
curl https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0-beta4/aio/deploy/recommended.yaml > recommended.yaml
# trap 1, Need deploy pod kubernetes-dashboard to the master, else loading is blocked.
# just open recommended.yaml, add nodeSelector as below
*** 184,193 ****
--- 184,195 ----
    template:
      metadata:
        labels:
          k8s-app: kubernetes-dashboard
      spec:
+       nodeSelector:
+         kubernetes.io/hostname: kubernetes-master
        containers:
          - name: kubernetes-dashboard
            image: kubernetesui/dashboard:v2.0.0-beta4
            imagePullPolicy: Always
            ports:
# deploy it
kubectl apply -f recommended.yaml
```

#### 3. Creating Sample User (in 192.168.22.55)
This is another trap (trap 2), Those the guide tell to create a sample user, but many people just ignore it.

```
# Create Service Account
kubectl create -f <
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kube-system
hit Ctrl-D to stop
```

```
kubectl create -f <
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kube-system
hit Ctrl-D to stop
```

#### 4. Run kubectl proxy (in 192.168.22.55)
```
kubectl proxy
```

#### 5. Open dashboard in browser
```
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
```

and token is
```
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')
```












