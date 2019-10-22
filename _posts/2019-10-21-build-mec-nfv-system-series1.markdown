---
layout: post
title:  "Build MEC/NFV System series 1 --- manage openstack over kubernetes"
categories: jekyll update
---

I write this series of posts to record the process when I am building the system to support deployment of MEC/NFV applications. It's just for personal study of MEC/NFV, to deep understand the architecture. 
This system have below targets,
1. Use Kubernetes to manage openstack service
2. Support Kubernetes Over Openstack VMs (Paas)
3. Support deployment of MEC/NFV applications

Below is the overview of infrastructure

![overview](/images/mec/architecture.jpeg)

  * Layer Hosts: This layer is composed of a group physical hosts or VMs which support nested vm, there has a kubernetes master, and others are kubernetes nodes. then openstack services can be ported into linux containers and are managed by kubernetes.
  * Layer Openstack: This layer uses one kubernetes node to act as openstack controller, and other nodes as openstack compute.
  * Layer VMs: Group of virtual machines that are managed by openstack
  * Layer Kubernetes clusters: Group of kubernetes clusters over openstack Iaas, to provide PaSS.

This post tells the process to setup openstack over kubernetes.

### Prepare Hosts
You need prepare at least three hosts, the host can be physical computer or virtual machine. If use virtual machine, the virtual machine need support nested vm as openstack will be installed to launch new vms.
```
# check if the hosts support kvm
# grep --color vmx /proc/cpuinfo
```
Note: for each hosts, 4 cpus and 4G memory are minimal requirements.

### Install kubernetes (Layer Hosts)
You need install a kubernetes master one one host, and kubernetes node on other hosts.
  * [Follow this link to install kubernetes](https://linuxconfig.org/how-to-install-kubernetes-on-ubuntu-18-04-bionic-beaver-linux) TODO


### Install Openstack (Layer Openstack)
You need choose one kubernetes node to install openstack controller, and other kubernetes nodes to install openstack compute.
  * [Follow this link to install openstack](https://docs.openstack.org/install-guide/environment.html) TODO


### Customize openstack services as kubernetes services (TODO)
We can package openstack services, especially nova and neutral services for compute node, into linux containers, as services or applications of kubernetes. 



