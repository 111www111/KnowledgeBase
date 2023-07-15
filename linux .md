# ps Hadoop100 等环境，这里！

## 硬件部分

虚拟机处理器配置

任务管理器 -》 性能 -》 cpu -》打开资源监控器

![](images/WEBRESOURCEd0013a3bf2b0db36d2f6cf1426d6a157.octet-stream)



![](images/WEBRESOURCEfe7e760fc2fd88d6e6b8e2e05700542d.octet-stream)

CPU15 一共16个CPU，

那么此时我们，处理器内核总数不要超过我们的最高数字16

![](images/WEBRESOURCE283523a5a3c3dd657a1c330d47807d46.octet-stream)

## 安装操作系统 略



![](images/WEBRESOURCEfe6b3f9c2d9000c8c9e1f819317a1215.octet-stream)

记得先查看一下虚拟化。

# 编辑vm网络环境

编辑-》编辑虚拟网络



![](images/WEBRESOURCE01134d04fceab9c98e5ac4745f778d2d.octet-stream)





![](images/WEBRESOURCEc3b86b658b39eb5673f403af2f02f2e2.octet-stream)



配置好网关，点击nat设置



![](images/WEBRESOURCE2a10654bd7f78ef21e3e8ff76e11ded2.octet-stream)



![](images/WEBRESOURCE44bbe202c23be75a30d57c57be52402f.octet-stream)

网关要和子网IP一个频段



# 配置windows网络



![](images/WEBRESOURCE8c83af837e6f60c5151f8f3493217c0d.octet-stream)

双击IPV4	

![](images/WEBRESOURCEecda1aea373c392b5d3b7e6f87faf9e2.octet-stream)

记得一致

![](images/WEBRESOURCE06c0976037e85e933d5ee7870cfb1b1f.octet-stream)

# 进入虚拟机系统

目录: vi /etc/sysconfig/network-scripts/ifcfg-ens33

```
TYPE="Ethernet"
PROXY_METHOD="none"
BROWSER_ONLY="no"
BOOTPROTO="static"
DEFROUTE="yes"
IPV4_FAILURE_FATAL="no"
IPV6INIT="yes"
IPV6_AUTOCONF="yes"
IPV6_DEFROUTE="yes"
IPV6_FAILURE_FATAL="no"
IPV6_ADDR_GEN_MODE="stable-privacy"
NAME="ens33"
UUID="833c0800-3d58-4b9c-b7ab-709d607d48d3"
DEVICE="ens33"
ONBOOT="yes"
IPADDR=192.168.10.101
GATEWAY=192.168.10.2
DNS1=192.168.10.2

```

配置映射

vim /etc/hosts

```
192.168.10.100 hadoop100
192.168.10.101 hadoop101
192.168.10.102 hadoop102
192.168.10.103 hadoop103
```

安装wget

```
yum install wget
```



# 配置JDK

```
#环境变量
cd /etc/profile.d
#创建自己的配置
vi my_env.sh 
```

内容

```
#JAVA_HOME
export JAVA_HOME=/java/jdk1.8.0_212
export PATH=$PATH:$JAVA_HOME/bin
```

适配

```
source /etc/profile
```



# 集群分发脚本

## 1）scp（secure copy）安全拷贝

（1）scp 定义

scp 可以实现服务器与服务器之间的数据拷贝。

（2）语法

```
scp  -r   $pdir/$fname          $user@$host:$pdir/$fname
命令 递归 要拷贝的文件路径/名称   目的地用户@主机:目的地路径/名称
```

# 防火墙        

## 一、防火墙的开启、关闭、禁用命令 

（1）设置开机启用防火墙：systemctl enable firewalld.service 

（2）设置开机禁用防火墙：systemctl disable firewalld.service 

（3）启动防火墙：systemctl start firewalld 

（4）关闭防火墙：systemctl stop firewalld 

（5）检查防火墙状态：systemctl status firewalld 

## 二、使用firewall-cmd配置端口 

（1）查看防火墙状态：firewall-cmd --state 

（2）重新加载配置：firewall-cmd --reload 

（3）查看开放的端口：firewall-cmd --list-ports 

（4）开启防火墙端口：firewall-cmd --zone=public --add-port=9200/tcp --permanent 

命令含义： 

–zone #作用域 

–add-port=9200/tcp #添加端口，格式为：端口/通讯协议 

–permanent #永久生效，没有此参数重启后失效 

注意：添加端口后，必须用命令firewall-cmd --reload重新加载一遍才会生效 

（5）关闭防火墙端口：firewall-cmd --zone=public --remove-port=9200/tcp --permanent                