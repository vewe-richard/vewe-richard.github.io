---
layout: post
title:  "Enterprise Gateway Use Cases"
categories: jekyll update
---
This post discusses how to use a Kubernetes managed edge gateway to supplement traditional enterprise Gateway. and is it possible to replace traditional enterprise gateway in the future?

我们需要找到一个痛点功能，能够接入到现有的企业网络中去，进入企业市场，并持续演化成核心设备？

# 1. about 100 users enterprise

Below is the network topology,
![Network topology](/images/sd-wan/100users.png)

Below is the architecture use docker engined edge gateway,
![GW Architecture](/images/sd-wan/100users-gw.png)

## 1.1 As Supplements
How to use a Kubernetes managed edge gateway to supplement traditional enterprise Gateway?

Find a breakthrough from below use cases.
### 1.1.1 Use cases

#### 1.1.1.1 wifi全覆盖

![wifi cover](/images/sd-wan/u1.png)

功能：vAC接入核心交换机trunk口，提供现有的网络隔离相应的wifi高速覆盖

问题：

结论：Is a breakthrough? NO


#### 1.1.1.2 Multi Path TCP

![mptcp](/images/sd-wan/u2.png)

功能：整合多种接入方式，提供更高的上网速度

问题：
   * 需要对端也支持MPTCP，所以主要是提升企业和分支之间的通信速率问题？
   * 根据应用分类对链路进行选择，比如数据专线，线路探测？然而提升网速，企业通常找的是网络运营商解决，缺少切入渠道?

结论：Is a breakthrough? NO


#### 1.1.1.3 Multi VPN 
功能：连接核心网交换机trunk口，根据现有的网路隔离环境提供相应的vpn服务。相较于现有VPN的方案，可以一个IP地址对应多个VPN，并且接入到每个隔离的网段。

问题：

   * 终归还是有软件的解决方案，可能有的需要申请更多的IP地址。
   * 这种需求并不是很迫切，有需要的个别部门，能找到软件的方案。
   * 通过适当的配置，传统路由器通常也提供VPN的接入，并且访问各个网段。

结论: Is a breakthrough? NO


#### 1.1.1.4 O.V.E.R SEA VPN 
功能：允许用户自行下载VPN容器，并提供方法租用Oversea云服务器，提供O.v.e.r sea vpn?

问题：
   * 法律问题, 不适合正规企业行为
   * 成本问题，对比员工自行购买VPN，成本偏高
   * 真全公司有需要的企业，或者可以进行申请？


结论: Is a breakthrough? NO



# 2. about 200 users enterprise


Below is the network topology,

![Network topology](/images/sd-wan/200users.png)


## 2.1 As Supplements
How to use a Kubernetes managed edge gateway to supplement traditional enterprise Gateway?

Find a breakthrough from below use cases.
### 2.1.1 Use cases

#### 2.1.1.1 ?



# 3. > 500 users enterprise
Below is the network topology,

![Network topology](/images/sd-wan/500users.png)


## 3.1 As Supplements
How to use a Kubernetes managed edge gateway to supplement traditional enterprise Gateway?

Find a breakthrough from below use cases.
### 3.1.1 Use cases

#### 3.1.1.1 ?



# 4. with branches enterprise
Below is the network topology,

![Network topology](/images/sd-wan/branchesusers.png)
![Network topology](/images/sd-wan/branchesusers2.png)


## 4.1 As Supplements
How to use a Kubernetes managed edge gateway to supplement traditional enterprise Gateway?

Find a breakthrough from below use cases.
### 4.1.1 Use cases

#### 4.1.1.1 SD-WAN
We put the sd-wan devices here,
![device location](/images/sd-wan/sd-wanlocation.png)



# Appendix
## A. SD-WAN Requirements
  * Need support SFP Port, and at least 4 SFP Ports































