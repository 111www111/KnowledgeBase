# 关于K8S让我重装4次虚拟机的故事

![](images/WEBRESOURCEdacda69f3398e6f8d02fe301456414fa.octet-stream)

# 一.K8S的概念和架构

## 1.1 K8S概述和特性

Kubernetes 是 Google 开源的一个容器编排引擎，它支持自动化部署、大规模可伸缩应用容器化管理。在生产环境中部署一个应用程序时，通常要部署该应用的多个实例以便对应用请求进行负载均衡;

在 Kubernetes 中，我们可以创建多个容器，每个容器里面运行一个应用实例，然后通过内置的负载均衡策略，实现对这一组应用实例的管理、发现、访问，而这些细节都不需要运维人员去进行复杂的手工配置和处理;

K8S的优点

（1）自动装箱基于容器对应用运行环境的资源配置要求自动部署应用容器

（2）自我修复(自愈能力)当容器失败时，会对容器进行重启当所部署的 Node 节点有问题时，会对容器进行重新部署和重新调度当容器未通过监控检查时，会关闭此容器直到容器正常运行时，才会对外提供服务

（4）水平扩展通过简单的命令、用户 UI 界面或基于 CPU 等资源使用情况，对应用容器进行规模扩大或规模剪裁

（5）服务发现用户不需使用额外的服务发现机制，就能够基于 Kubernetes 自身能力实现服务发现和负载均衡

（6）滚动更新可以根据应用的变化，对应用容器运行的应用，进行一次性或批量式更新

（7）版本回退可以根据应用部署情况，对应用容器运行的应用，进行历史版本即时回退

（8）密钥和配置管理在不需要重新构建镜像的情况下，可以部署和更新密钥和应用配置，类似热部署。

（9）存储编排自动实现存储系统挂载及应用，特别对有状态应用实现数据持久化非常重要存储系统可以来自于本地目录、网络存储(NFS、Gluster、Ceph 等)、公共云存储服务

（10）批处理提供一次性任务，定时任务；满足批量数据处理和分析的场景

## 1.2 K8S架构组件

架构图

![](images/WEBRESOURCEda5508980c94dcf23a98dc2f6858658c.octet-stream)

### 1.2.1 MasterNode组件

ApiServer:集群的统一入口,以restful方式,交给ETCD进行存储

Scheduler:节点调度,类似yern,选择node进行应用部署

Controller-manager: 处理集群中常规后台任务,一个资源对应一个控制器

简单来说,假如我有一个订单服务,那么在controller中会建立一个针对于订单的conroller进行此服务的管理

ETCD: 一种存储系统,用于保存集群中的各种数据.

### 1.2.2 WorkNode组件

Kubelet: Master中,管理Node结点容器状态的组件,例如包的数据,创建容器,生命周期等.

简单理解就是包工头(master)派到现场(work)的小工.负责管理现场的一堆工人.

Kube-Proxy: 提供网络代理,可以实现负载均衡等操作.

## 1.3 K8S核心概念

Pod: 

1. pod在K8S中为最小的部署单元,

2. 一个pod中存在一组容器的集合

3. 一个pod中的容器是共享网络的,

4. pod的生命周期是短暂的;

Controller:  

1. 确保预期的Pod副本数量

2. 无状态应用部署

3. 有状态应用部署(有特定条件,例如网络、IP)

4. 可以确保所有Node都运行同一个pod

5. 一次任务和定时任务

Server: 

1.定义一组pod的访问规则

# 二、	使用kubeadm安装k8s

## 1.卸载K8S

```
#!/bin/bash
kubeadm reset -f
modprobe -r ipip
lsmod
rm -rf ~/.kube/
rm -rf /etc/kubernetes/
rm -rf /etc/systemd/system/kubelet.service.d
rm -rf /etc/systemd/system/kubelet.service
rm -rf /usr/bin/kube*
rm -rf /etc/cni
rm -rf /opt/cni
rm -rf /var/lib/etcd
rm -rf /var/etcd
yum -y remove kubeadm* kubectl* kubelet* docker*
reboot
```

## 2.开始安装

在安装之前，我们先来设计下集群的角色问题，以我的hadoopxxx为标准，设计如下

| 角色 | IP |
| - | - |
| node1(master) | 192.168.10.100 |
| node2(worker) | 192.168.10.101 |
| node3(worker) | 192.168.10.102 |


## 3.准备工作

### 关闭防火墙

```
systemctl stop firewalld
systemctl disable firewalld

```

### 关闭selinux

```
setenforce 0  # 临时
sed -i 's/enforcing/disabled/' /etc/selinux/config  # 永久

```

### 关闭swap

```
swapoff -a  # 临时
sed -ri 's/.*swap.*/#&/' /etc/fstab    # 永久

```

### 根据规划设置主机名

```
hostnamectl set-hostname <hostname>

```

### 在master添加hosts

```
cat >> /etc/hosts << EOF
192.168.18.128 master
192.168.18.138 node1
192.168.18.148 node2
EOF

```

### 将桥接的IPv4流量传递到iptables的链

```
cat > /etc/sysctl.d/k8s.conf << EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sysctl --system  # 生效

```

### 时间同步

```
yum install ntpdate -y
ntpdate time.windows.com

```

### 安装docker

> 
Kubernetes默认CRI（容器运行时）为Docker，因此先安装Docker。


如果docker已经安装，先卸载docker

```
yum list installed | grep docker
yum remove docker.x86_64

```

安装docker

```
yum install docker

```

> 
安装docker


查看docker版本

```
docker version

```

启动docker

```
systemctl start docker & systemctl enable docker

```

为docker添加阿里云镜像仓库

```go
cat > /etc/docker/daemon.json << EOF
{
  "registry-mirrors": ["https://b9pmyelo.mirror.aliyuncs.com"]
}
EOF

```

## 添加阿里云YUM软件源

添加阿里云YUM软件源，为安装kubeadm，kubelet和kubectl做准备。

```
cat > /etc/yum.repos.d/kubernetes.repo << EOF
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=0
repo_gpgcheck=0
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF

```

## 安装kubeadm，kubelet和kubectl

> 

kubeadm：用来初始化集群的指令。
kubelet：在集群中的每个节点上用来启动 Pod 和容器等。
kubectl：用来与集群通信的命令行工具。



```
yum install -y kubelet-1.23.0 kubeadm-1.23.0 kubectl-1.23.0   
systemctl enable kubelet

```

## 部署Kubernetes Master

查看 kubeadm版本

```
kubeadm version

kubeadm version: &version.Info{Major:"1", Minor:"23", GitVersion:"v1.23.5", GitCommit:"c285e781331a3785a7f436042c65c5641ce8a9e9", GitTreeState:"clean", BuildDate:"2022-03-16T15:57:37Z", GoVersion:"go1.17.8", Compiler:"gc", Platform:"linux/amd64"}

```

部署master（只在master上执行初始化）

```
kubeadm init \
  --apiserver-advertise-address=192.168.10.100 \
  --image-repository registry.aliyuncs.com/google_containers \
  --service-cidr=10.96.0.0/12 \
  --pod-network-cidr=10.244.0.0/16

```

> 
由于默认拉取镜像地址k8s.gcr.io国内无法访问，这里指定阿里云镜像仓库地址。


## 使用kubectl工具

### master

```sh
mkdir -p $HOME/.kube
cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
chown $(id -u):$(id -g) $HOME/.kube/config
echo 'export KUBECONFIG=$HOME/.kube/config' >> $HOME/.bashrc
source ~/.bashrc

```

## 在master上部署CNI网络插件

```
wget https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

```

默认镜像地址无法访问，sed命令修改为docker hub镜像仓库。

```
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

kubectl get pods -n kube-system
NAME                          READY   STATUS    RESTARTS   AGE
kube-flannel-ds-amd64-2pc95   1/1     Running   0          72s

```

## 加入Kubernetes Node

> 
在（worker）上执行，即node2和node3


向集群添加新节点，执行在kubeadm init输出的kubeadm join命令：

```
$ kubeadm join 192.168.18.128:6443 --token esce21.q6hetwm8si29qxwn \
    --discovery-token-ca-cert-hash sha256:00603a05805807501d7181c3d60b478788408cfe6cedefedb1f97569708be9c5

```

> 
注意：不要直接拷贝文档，而是拷贝你安装界面输出的结果。


默认token有效期为24小时，当过期之后，该token就不可用了。这时就需要重新创建token，操作如下：

```
kubeadm token create --print-join-command

```

## 在集群中部署容器

在Kubernetes集群中创建一个pod，验证是否正常运行：

```
$ kubectl create deployment nginx --image=nginx
$ kubectl expose deployment nginx --port=80 --type=NodePort
$ kubectl get pod,svc

```

测试

```
http://192.168.18.128:32446/

```

