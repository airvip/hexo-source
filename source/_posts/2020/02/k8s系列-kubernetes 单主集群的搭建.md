---
title: k8s系列-kubernetes 单主集群的搭建
date: 2020-02-07 09:01:00
tags: 
    - Kubernetes
    - K8s
    - Docker
---

> 愿你有好运，如果没有。希望你在慈悲中学会坚强。

Kubernetes 是用于自动部署，扩展和管理容器化应用程序的开源系统，该项目托管在 [CNCF](https://www.cncf.io/about)。

<!-- more -->

[官方教程了解一下](https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/)


## [What] Kubernetes 是什么

Kubernetes 是一个可移植的、可扩展的开源平台，用于管理容器化的工作负载和服务，可促进声明式配置和自动化。Kubernetes 拥有一个庞大且快速增长的生态系统。Kubernetes 的服务、支持和工具广泛可用。

名称 Kubernetes 源于希腊语，意为 “舵手” 或 “飞行员”。Google 在 2014 年开源了 Kubernetes 项目。Kubernetes 建立在 [Google 在大规模运行生产工作负载方面拥有十几年的经验的基础上](http://queue.acm.org/detail.cfm?id=2898444)，结合了社区中最好的想法和实践。

应用部署发展史

![应用部署发展史](https://d33wubrfki0l68.cloudfront.net/26a177ede4d7b032362289c6fccd448fc4a91174/eb693/images/docs/container_evolution.svg)


## [Why] 为什么使用 Kubernetes

Kubernetes 提供了：

* 服务发现和负载均衡

Kubernetes 可以使用 DNS 名称或自己的 IP 地址公开容器，如果到容器的流量很大，Kubernetes 可以负载均衡并分配网络流量，从而使部署稳定。

* 存储编排

Kubernetes 允许您自动挂载您选择的存储系统，例如本地存储、公共云提供商等。

* 自动部署和回滚

可以使用 Kubernetes 描述已部署容器的所需状态，它可以以受控的速率将实际状态更改为所需状态。例如，您可以自动化 Kubernetes 来为您的部署创建新容器，删除现有容器并将它们的所有资源用于新容器。

* 自动二进制打包

Kubernetes 允许您指定每个容器所需 CPU 和内存（RAM）。当容器指定了资源请求时，Kubernetes 可以做出更好的决策来管理容器的资源。

* 自我修复

Kubernetes 重新启动失败的容器、替换容器、杀死不响应用户定义的运行状况检查的容器，并且在准备好服务之前不将其通告给客户端。

* 密钥与配置管理

Kubernetes 允许您存储和管理敏感信息，例如密码、OAuth 令牌和 ssh 密钥。您可以在不重建容器镜像的情况下部署和更新密钥和应用程序配置，也无需在堆栈配置中暴露密钥。

## [How] 如何使用 Kubernetes

我们将通过创建一个**单主集群**的方式来快速入门，集群节点为 1 主 2 从模式

### 准备

虚拟机基本要求，如下：

* OS：Ubuntu Server X64 16.04 LTS 版本
* CPU：最低要求，1 CPU 2 核
* 内存：最低要求，2GB
* 磁盘：最低要求，20GB

创建三台虚拟机，如下：

* 名：k8s-master  IP：192.168.226.139
* 名：k8s-slave1  IP：192.168.226.140
* 名：k8s-slave2  IP：192.168.226.141

对虚拟机系统的配置：

* 关闭交换空间：`sudo swapoff -a`
* 避免开机启动交换空间：注释 /etc/fstab 中的 swap
* 关闭防火墙：ufw disable

### 安装 Docker

[Ubunt Sevrver 16.04 LTS 安装 docker](http://blog.diff.wang/2019/08/Linux%E5%AE%89%E8%A3%85docker.html)

### 修改主机名

```
# 使用 hostnamectl 命令修改，其中 k8s-master 为新的主机名
root@ubuntu:~# hostnamectl set-hostname k8s-master

root@k8s-master:~# hostnamectl
# 显示如下内容
   Static hostname: k8s-master
Transient hostname: tl
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 3808e4de0ea404098723fb995e3de733
           Boot ID: d24ad8aacc93404a84b0f0187a6edc07
    Virtualization: vmware
  Operating System: Ubuntu 16.04 LTS
            Kernel: Linux 4.4.0-177-generic
      Architecture: x86-64
```

另外两台分别修改成对应的 k8s-slave1，k8s-slave2

### 设置静态 IP

```
root@k8s-master:~# vim /etc/network/interfaces

修改为如下内容

# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
auto ens33
#iface ens33 inet dhcp
iface ens33 inet static
# IP 地址
address 192.168.226.139
# 子网掩码
netmask 255.255.255.0
# 网关 (设置自己的网关地址)
gateway 192.168.226.2
dns-nameserver 114.114.114.114
```

另外两台分别修改成对应的 k8s-slave1 192.168.226.140，k8s-slave2 192.168.226.141

### 安装 kubeadm，kubelet，kubectl

```
# 安装系统工具
root@k8s-master:~# apt-get update && apt-get install -y apt-transport-https

# 安装 GPG 证书
root@k8s-master:~# curl https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | apt-key add -

# 写入软件源
root@k8s-master:~# cat << EOF >/etc/apt/sources.list.d/kubernetes.list
> deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main
> EOF

# 更新软件源
root@k8s-master:~# apt-get update  

# 安装 kubeadm，kubelet，kubectl(注意版本号)
apt-get install -y kubelet kubeadm kubectl

# 设置 kubelet 自启动，并启动 kubelet
systemctl enable kubelet && systemctl start kubelet
```

另外两台分别同样步骤安装

* kubeadm：用于初始化 Kubernetes 集群
* kubectl：Kubernetes 的命令行工具，主要作用是部署和管理应用，查看各种资源，创建，删除和更新组件
* kubelet：主要负责启动 Pod 和 容器

![k8s_let_adm_ctl](https://s1.ax1x.com/2020/04/25/J6Vcod.png)

### 配置 kubeadm

安装 kubernetes 主要是安装它的各个镜像，而 kubeadm 已经为我们集成好了运行 kubernetes 所需的基本镜像。但由于国内的网络原因，我们修改为阿里云提供的镜像服务即可。

```
root@k8s-master:~# cd /usr/local/
root@k8s-master:/usr/local# mkdir -p docker/kubernetes
root@k8s-master:/usr/local# cd docker/kubernetes

# 导出配置文件
root@k8s-master:/usr/local/docker/kubernetes# kubeadm config print init-defaults --kubeconfig ClusterConfiguration > kubeadm.yml

# 修改配置
root@k8s-master:/usr/local/docker/kubernetes# vim kubeadm.yml

主要修改内容如下
# 修改了镜像地址
imageRepository: registry.aliyuncs.com/google_containers
kind: ClusterConfiguration
kubernetesVersion: v1.18.2
networking:
  dnsDomain: cluster.local
  # 修改了 pod 子网，后面安装 Pod 网络插件 Calico 也设置为同样的网段
  podSubnet: 10.244.0.0/16



# 查看所需镜像列表
root@k8s-master:/usr/local/docker/kubernetes# kubeadm config images list --config kubeadm.yml
# 拉取镜像
root@k8s-master:/usr/local/docker/kubernetes# kubeadm config images pull --config kubeadm.yml

```

### 使用 kubeadm 搭建 kubernetes 集群

初始化主节点，
* --config 指定了初始化时需要使用的配置文件
*  --upload-certs 参数可以在后续执行加入节点时自动分发证书文件。
* tee kubeadm-init.log 用以输出日志。

```
root@k8s-master:/usr/local/docker/kubernetes# kubeadm init --config=kubeadm.yml --upload-certs | tee kubeadm-init.log
```

kubeadm init 的执行过程
* init：指定版本进行初始化操作
* preflight：初始化前的检查和下载所需要的 Docker 镜像文件
* kubelet-start：生成 kubelet 的配置文件 var/lib/kubelet/config.yaml，没有这个文件 kubelet 无法启动，所以初始化之前的 kubelet 实际上启动不会成功
* certificates：生成 Kubernetes 使用的证书，存放在 /etc/kubernetes/pki 目录中
* kubeconfig：生成 KubeConfig 文件，存放在 /etc/kubernetes 目录中，组件之间通信需要使用对应文件
* control-plane：使用 /etc/kubernetes/manifest 目录下的 YAML 文件，安装 Master 组件
* etcd：使用 /etc/kubernetes/manifest/etcd.yaml 安装 Etcd 服务
* wait-control-plane：等待 control-plan 部署的 Master 组件启动
* apiclient：检查 Master 组件服务状态。
* uploadconfig：更新配置
* kubelet：使用 configMap 配置 kubelet
* patchnode：更新 CNI 信息到 Node 上，通过注释的方式记录
* mark-control-plane：为当前节点打标签，打了角色 Master，和不可调度标签，这样默认就不会使用 Master 节点来运行 Pod
* bootstrap-token：生成 token 记录下来，后边使用 kubeadm join 往集群中添加节点时会用到
* addons：安装附加组件 CoreDNS 和 kube-proxy

![k8s_master_init](https://s1.ax1x.com/2020/04/25/J6mKJA.png)

### 配置 kubectl


```
root@k8s-master:/usr/local/docker/kubernetes# mkdir -p $HOME/.kube

root@k8s-master:/usr/local/docker/kubernetes# cp -i /etc/kubernetes/admin.conf $HOME/.kube/config

root@k8s-master:/usr/local/docker/kubernetes# chown $(id -u):$(id -g) $HOME/.kube/config

# 检验是否成功
root@k8s-master:/usr/local/docker/kubernetes# kubectl get nodes
NAME         STATUS     ROLES    AGE   VERSION
k8s-master   NotReady   master   76s   v1.18.2

root@k8s-master:/usr/local/docker/kubernetes# kubectl get nodes
NAME         STATUS   ROLES    AGE     VERSION
k8s-master   Ready    master   7m32s   v1.18.2
```

### 安装 Pod 网络插件 Calico

[官网 Installing a Pod network add-on](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/#pod-network)


```
root@k8s-master:/usr/local/docker/kubernetes# wget https://docs.projectcalico.org/v3.11/manifests/calico.yaml


# 编辑网段
root@k8s-master:/usr/local/docker/kubernetes# vim calico.yaml

calico 默认网段 192.168.0.0/16 ，我们修改为 10.224.0.0/16

行号            内容
625             - name: CALICO_IPV4POOL_CIDR
626               value: "10.224.0.0/16"

# 配置网络插件
root@k8s-master:/usr/local/docker/kubernetes# kubectl apply -f calico.yaml

# 测试安装是否成功
root@k8s-master:/usr/local/docker/kubernetes# watch kubectl get pods --all-namespaces
```
STATUS 全部 running

![kube_watch_pod](https://s1.ax1x.com/2020/04/26/J63sIK.png)


### 将 slave 加入到集群

这两台 slave, 其实应该叫做 node 节点，执行后续操作前默认已经走过了下面的四个步骤：

1. 安装 docker
2. 修改主机名
3. 设置静态 IP
4. 安装 kubeadm，kubelet，kubectl

```
root@k8s-slave1:~# kubeadm join 192.168.226.139:6443 --token abcdef.0123456789abcdef --discovery-token-ca-cert-hash sha256:ff829251c7b8b73cb66be9341da6e9f870cc11a5e92668ce1a92dfd3e07f1934


root@k8s-slave2:~# kubeadm join 192.168.226.139:6443 --token abcdef.0123456789abcdef --discovery-token-ca-cert-hash sha256:ff829251c7b8b73cb66be9341da6e9f870cc11a5e92668ce1a92dfd3e07f1934
```

> 如果 slave 节点加入 master 时配置有问题可以在 slave 节点上使用 kubeadm reset 重置配置再使用 kubeadm join 命令重新加入即可。在 master 节点删除 node ，可以使用 kubeadm delete nodes <NAME> 删除。

> 如果 token 过期，我们可以在主节点上查看或者重新生成

```
# 打印出 token 信息
root@k8s-master:~# kubeadm token list
# 创建新的 token
root@k8s-master:~# kubeadm token create

discovery-token-ca-cert-hash 值
1. 可以通过安装 master 时的日志查看 sha256 信息
2. 可以通过 openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | openssl dgst -sha256 -hex | sed 's/^.* //' 命令查看 sha256 信息
```

在主节点查看是否加入 node 节点成功

```
# 查看 node 节点
root@k8s-master:~# kubectl get nodes

# 查看 pod 状态
root@k8s-master:~# kubectl get pods -A
root@k8s-master:~# kubectl get pods -n kube-system
```

![kubectl_get_nodes](https://s1.ax1x.com/2020/04/26/J63Xss.png)


OK !!! 至此，部署完毕！

## 重置(为了写博客)

k8s-slave2

```
root@k8s-slave2:~# kubeadm reset
[reset] WARNING: Changes made to this host by 'kubeadm init' or 'kubeadm join' will be reverted.
[reset] Are you sure you want to proceed? [y/N]: y
[preflight] Running pre-flight checks
W0426 00:52:25.850321   65170 removeetcdmember.go:79] [reset] No kubeadm config, using etcd pod spec to get data directory
[reset] No etcd config found. Assuming external etcd
[reset] Please, manually reset etcd to prevent further issues
[reset] Stopping the kubelet service
[reset] Unmounting mounted directories in "/var/lib/kubelet"
[reset] Deleting contents of config directories: [/etc/kubernetes/manifests /etc/kubernetes/pki]
[reset] Deleting files: [/etc/kubernetes/admin.conf /etc/kubernetes/kubelet.conf /etc/kubernetes/bootstrap-kubelet.conf /etc/kubernetes/controller-manager.conf /etc/kubernetes/scheduler.conf]
[reset] Deleting contents of stateful directories: [/var/lib/kubelet /var/lib/dockershim /var/run/kubernetes /var/lib/cni]

The reset process does not clean CNI configuration. To do so, you must remove /etc/cni/net.d

The reset process does not reset or clean up iptables rules or IPVS tables.
If you wish to reset iptables, you must do so manually by using the "iptables" command.

If your cluster was setup to utilize IPVS, run ipvsadm --clear (or similar)
to reset your system's IPVS tables.

The reset process does not clean your kubeconfig files and you must remove them manually.
Please, check the contents of the $HOME/.kube/config file.

root@k8s-slave2:~# cd /etc/
root@k8s-slave2:/etc# rm -rf cni/
root@k8s-slave2:/etc# 

```

k8s-slave1

```
root@k8s-slave1:~# kubeadm reset
[reset] WARNING: Changes made to this host by 'kubeadm init' or 'kubeadm join' will be reverted.
[reset] Are you sure you want to proceed? [y/N]: y
...
root@k8s-slave1:~# cd /etc/
root@k8s-slave1:/etc# rm -rf cni/
root@k8s-slave1:/etc#
```

k8s-master

```
root@k8s-master:~# kubeadm reset
[reset] Reading configuration from the cluster...
[reset] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -oyaml'
[reset] WARNING: Changes made to this host by 'kubeadm init' or 'kubeadm join' will be reverted.
[reset] Are you sure you want to proceed? [y/N]: y
...
root@k8s-master:~# rm -rf .kube/
root@k8s-master:~# cd /etc/
root@k8s-master:/etc# rm -rf cni/
```