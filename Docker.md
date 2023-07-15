# 天上飞的理念,地上落地实现

# windows docker

必要安装文件

[wsl_update_x64.msi](attachments/WEBRESOURCE2655218be6095a69aa6323ce7a6d9586wsl_update_x64.msi)

# 1.docker安装(centos7)

https://docs.docker.com/engine/install/centos/

## 1.1卸载旧版本

```
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```

## 1.2yum 安装 gcc

```
首先,你的linux得能联网
yum -y install gcc
yum -y install gcc-c++
```

## 1.3安装docker

```
#安装docker储存库
sudo yum install -y yum-utils
#设置镜像仓库
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
#当然,这里可以更换国内源
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo    

#更新yum,以防yum出现问题
yum makecache fast

#安装docker!!
sudo yum install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

## 1.4 启动docker 

```
systemctl start docker

#查看版本
docker version

#跑个helloworld
docker run hello-world
#这里的docker run,先寻找了本机有没有这个镜像,
#如果有就生产一个容器,如果没有,则去docker hub上寻找镜像运行.
```

![](images/WEBRESOURCEcfd01b10eb0e0e622518ab0d3cec9be1.octet-stream)

安装成功

# 2. docker的命令!

## 2.1 帮助启动类

```
#启动docker
systemctl start docker
#停止docker
systemctl stop docker
#重启docker
systemctl restart docker
#查看docker状态
systemctl status docker
#设置开机启动
systemctl enable docker
#查看docker概要信息
docker info
#查看命令帮助文档
docker 具体命令 --help
```

## 2.2 镜像命令

```
#列出本地主机上的镜像
docker images
```



![](images/WEBRESOURCE3fd1d308263edfba55e5c290be83196f.octet-stream)

PERPOSITORY : 镜像的仓库源

TAG : 镜像标签(版本号)

IMAGE ID : 镜像ID

CREATED : 镜像创建时间

SIZE : 镜像大小

```
#查看某个镜像版本
docker search 镜像名
#拉取某个镜像
docker pull 镜像名:版本号
#统计信息
docker system df
#删除镜像 -f为强制删除
docker rmi -f 镜像名
```

## 2.3 容器命令

```
#新建+启动容器
docker run 参数 镜像名称
#上述参数部分
    容器命名:--name=容器名
    后台运行容器:-d
    交互模式运行容器: -i
    为容器重新分配一个终端: -t
    指定端口映射 : -p 容器外端口:容器内端口
    随机端口映射 : -P
    
#查看所有运行的容器
docker ps 参数
#上述参数部分
    运行过的(包含历史):-a
    显示最近创建的 : -l
    静默模式,只显示编号: -q

#退出容器
exit -> 容器会停止
ctrl+p+q -> 容器会后台运行          

#启动已经停止运行的容器
docker start 容器id或者容器名

#重启容器
docke restart 容器id或者容器名

#停止容器
docker stop 容器id或者容器名

#强制停止容器
docker kill 容器id或者容器名 

#删除已停止的容器 -f为强制
docker rm -f 容器id      

                                                                                          
                                                                                                                                                                                    # 开启容器自启动
docker update --restart=always 【容器名】
例如：docker update --restart=always tracker


# 关闭容器自启动
docker update --restart=no【容器名】
例如：docker update --restart=no tracker

##### 相关配置解析
no：
    不要自动重启容器。（默认）

on-failure： 
    如果容器由于错误而退出，则重新启动容器，该错误表现为非零退出代码。

always：
    如果容器停止，请务必重启容器。如果手动停止，则仅在Docker守护程序重新启动或手动重新启动容器本身时才重新启动。（参见重启政策详情中列出的第二个项目）

unless-stopped：
    类似于always，除了当容器停止（手动或其他方式）时，即使在Docker守护程序重新启动后也不会重新启动容器。
                                                                                          
```

## 2.4 常用 重要命令

```
#启动守护容器,我们希望服务在后台运行
docker run -d 镜像名
#这里要注意一点,我们用乌班图举例,后台运行后会自动退出,在docker中,后台运行必须有一个前台进程

#查看容器日志
docker logs 容器id或名称
docker logs -f --tail 500 容器ID

#查看容器性能
docker top 容器id或名称

#进入正在运行的容器
docker exec -it bash

#从容器内拷贝文件到主机
docker cp 容器id或name: 容器内路径 容器外路径    

#导出容器
docker export 容器id或名称 > 路径/文件名.tar

#导入
cat 文件名.tar | docker import - 镜像用户/镜像名:镜像版本号
#例
cat redis01.tar | docker import - root/redis:6.0.8
```

# 3.docker镜像

## 3.1 docker镜像是什么?

镜像

是一种轻量级、可执行的独立软件包，它包含运行某个软件所需的所有内容，我们把应用程序和配置依赖打包好形成一个可交付的运行环境(包括代码、运行时需要的库、环境变量和配置文件等)，这个打包好的运行环境就是image镜像文件。

只有通过这个镜像文件才能生成Docker容器实例(类似Java中new出来一个对象)。

## 3.2 分层镜像(联合文件系统)

简单一说

我们以一个pull的镜像为例

![](images/WEBRESOURCE4b849894592d92e80d48447225d8eedb.octet-stream)

这个tomcat,是一层一层的在下载

联合文件系统是一种分层,轻量级的文件系统,支持对文件系统的修改作为一次提交层层叠加,同时可以将不同目录挂载到同一个虚拟文件系统

特性:一次性加载多个文件系统,但从整体上看,只能看到一个文件系统

docker实质上就是一层一层的文件系统组成,而每个文件都是最精简最核心的内容,例如linux,所以docker的很多东西都很小,所以下载很快.

## 3.3 commit 命令

我们以ubuntu举例,官方镜像中是不内置vim编辑器,我们安装后尝试commit

```
#运行乌班图
docker run -it ubuntu bash

#安装vim
apt-get update
apt-get -y install vim

#commit我们自己的新镜像
docker commit -m="描述" -a="作者" 容器id 创建目标镜像名:[标签] 
```

下图所示,提交成功

![](images/WEBRESOURCEbef5259832eebbab52029c194d6c5330.octet-stream)

# 4.容器数据卷

## 4.1尝试创建一个容器卷

```
#深坑,容器卷记得加入!!!!
--privileged=true
```

什么是容器数据卷?

相当于将容器内数据持久化,可以将容器内的数据备份＋持久化到本地主机目录

- 数据卷可在容器之间共享或重用数据

- 卷中的更改可以直接实时生效

- 数据卷的更改不会包含在镜像的更新中

- 数据卷的生命周期一直持续到没有容器使用为止

```
#语法
docker run -it --privileged=true -v /宿主机绝对路径目录:/容器内目录:参数 镜像名
#参数 
只读:ro
读写:rw
```

当该容器运行时,无论是在主机还是容器修改目录下的文件,会双向绑定,类似vue的概念,本质其实就是指向同一个地址.

如果你忘记了你的容器绑定的数据卷在哪里,可以尝试下列命令

```
docker inspect 容器名或者id
```

在结果中寻找这一项 Source 主机目录,Destination 容器目录

```json
"Mounts": [
            {
                "Type": "bind",
                "Source": "/wyt/ubuntu",
                "Destination": "/tmp/docker_data",
                "Mode": "",
                "RW": true,
                "Propagation": "rprivate"
            }
        ],

```

容器卷是可以存在继承关系的

例如刚才我们的乌班图u1,如果我们需要再创建一个乌班图,使用同一个数据卷,可以这样做

```
docker run -it --privileged=true --volumes-from 继承的容器名 镜像名
```

# 5. docker常规软件安装

总体步骤,找镜像,拉镜像,配置启动,完事

## 5.1 Tomcat的安装

```
#获取tomcat镜像
docker pull tomcat:版本号

#创建容器实例
docker run -d -p 8080:8080 tomcat
```

可能存在的问题

页面404 可能没有映射端口或者没有关闭防火墙

当然,页面可能还是404.原因:docker压缩镜像,webapps中没有存放东西,网页在webapps.dist中,我们只需要把webapps删除后重命名即可

```
mv webapps.dist webapps
```

![](images/WEBRESOURCE23f5ce0ccce1976c840651fd1a44dec8.octet-stream)

启动成功

![](images/WEBRESOURCE4968adae47830957e59f5b727a97903f.octet-stream)

当然 这里也有免修改版的tomcat

```
docker pull billygoo/tomcat8-jdk8
docker run -d -p 8080:8080 --name tomcat8 billygoo/tomcat8-jdk8
```

免修改运行

![](images/WEBRESOURCE8e63a30455d3e19d3c8d39d7ff70b5e3.octet-stream)

## 5.2 MySQL的安装

https://hub.docker.com/_/mysql

```
#开启一个mysql实例 -e后面是root的密码.
docker run -p 3306:3306 --name some-mysql -e MYSQL_ROOT_PASSWORD=123@qwe -d mysql:tag
```

![](images/WEBRESOURCEab2fd91589ded46b11385c04338ec504.octet-stream)

尝试插入一条数据呢?

![](images/WEBRESOURCE27c44affc51782cfe800d97ba998127d.octet-stream)

那么此时,问题来了.

### 5.2.1 删除容器,mysql没数据了!

这里需要部署数据卷,

```
#创建mysql实例 附加数据卷
docker run -d -p 3306:3306 --privileged=true 
-v /wyt/mysql/mysql01/log:/var/log/mysql 
-v /wyt/mysql/mysql01/data:/var/lib/mysql 
-v /wyt/mysql/mysql01/conf:/etc/mysql/conf.d 
-e MYSQL_ROOT_PASSWORD=123@qwe 
--name mysql01 mysql:8.0
```

### 5.2.2 存在的问题一,无法插入中文

![](images/WEBRESOURCEd6a134db5017a1a72bd464d37aa34fed.octet-stream)

问题原因:docker编码没有修正,全部为拉丁

```
#查看编码
 SHOW VARIABLES LIKE 'character%'
```

![](images/WEBRESOURCE71f1c58485af5921d06c72af777ce437.octet-stream)

解决方案:

```
#在上方容器数据卷配置文件中创建一个自己的配置文件 my.cnf ,设置如下
[client]
default_character_set=utf8
[mysqld]
collation_server = utf8_general_ci
character_set_server = utf8
```

重启容器查看即可

```
docker restart mysql01
```

![](images/WEBRESOURCE5f19fccf9eaa2a1a483ab1e786a591bd.octet-stream)

### 5.2.3 docker搭建mysql主从复制

目标:

3306 mysql01 主机

3307 mysql02 从机



我们刚才弄了个mysql01,现在做一个mysql02

```
#创建mysql实例 附加数据卷
docker run -d -p 3307:3306 --privileged=true 
-v /wyt/mysql/mysql02/log:/var/log/mysql 
-v /wyt/mysql/mysql02/data:/var/lib/mysql 
-v /wyt/mysql/mysql02/conf:/etc/mysql/conf.d 
-e MYSQL_ROOT_PASSWORD=123@qwe 
--name mysql02 mysql:5.7
```

我们进入mysql01配置下新建my.cnf

```
[mysqld]
## 设置server_id，同一局域网中需要唯一
server_id=101 
## 指定不需要同步的数据库名称
binlog-ignore-db=mysql  
## 开启二进制日志功能
log-bin=mall-mysql-bin  
## 设置二进制日志使用内存大小（事务）
binlog_cache_size=1M  
## 设置使用的二进制日志格式（mixed,statement,row）
binlog_format=mixed  
## 二进制日志过期清理时间。默认值为0，表示不自动清理。
expire_logs_days=7  
## 跳过主从复制中遇到的所有错误或指定类型的错误，避免slave端复制中断。
## 如：1062错误是指一些主键重复，1032错误是因为主从数据库数据不一致
slave_skip_errors=1062
```

重启mysql01

```
docker restart mysql01
```

进入mysql01容器

```
docker exec -it mysql01 /bin/bash
```

创建一个数据库同步用户

```
CREATE USER 'slave'@'%' IDENTIFIED BY '123@qwe';
GRANT REPLICATION SLAVE, REPLICATION CLIENT ON *.* TO 'slave'@'%';
```

接下来前往从机 mysql02的数据卷中,创建my.cnf,内容如下

```
[mysqld]
## 设置server_id，同一局域网中需要唯一
server_id=102
## 指定不需要同步的数据库名称
binlog-ignore-db=mysql  
## 开启二进制日志功能，以备Slave作为其它数据库实例的Master时使用
log-bin=mall-mysql-slave1-bin  
## 设置二进制日志使用内存大小（事务）
binlog_cache_size=1M  
## 设置使用的二进制日志格式（mixed,statement,row）
binlog_format=mixed  
## 二进制日志过期清理时间。默认值为0，表示不自动清理。
expire_logs_days=7  
## 跳过主从复制中遇到的所有错误或指定类型的错误，避免slave端复制中断。
## 如：1062错误是指一些主键重复，1032错误是因为主从数据库数据不一致
slave_skip_errors=1062  
## relay_log配置中继日志
relay_log=mall-mysql-relay-bin  
## log_slave_updates表示slave将复制事件写进自己的二进制日志
log_slave_updates=1  
## slave设置为只读（具有super权限的用户除外）
read_only=1
```

重启mysql 02

```
docker restart mysql02
```

先去主数据库mysql01查看binlog日志

```
show master status;
```

![](images/WEBRESOURCE00b10e878e89c2e144400a27da1b1b4d.octet-stream)

然后进入mysql02

```
docker exec -it mysql02 /bin/bash
```

下列配置,都在从机 mysql02;

配置主从复制

注意 log_pos为上图的postition

```
change master to master_host='192.168.10.100', 
master_user='slave', 
master_password='123@qwe', 
master_port=331, 
master_log_file='mall-mysql-bin.000001', 
master_log_pos=157, 
master_connect_retry=30,
#mysql 8.0 以上，需要加这个参数
GET_MASTER_PUBLIC_KEY=1;
```

数据解析

![](images/WEBRESOURCE2edaa0fb61a743938d858c38161f1e91.octet-stream)

查看下从库状态

```
show slave status \G;
```

![](images/WEBRESOURCE28391407cf0237b8cc763534f0d9dd6a.octet-stream)

在从库开启主从同步!

```
start slave;
```

查看状态:双YES

![](images/WEBRESOURCE53685d363984d515f961952e7fad838e.octet-stream)

# 6.dockerFile

## 6.1DockerFile保留字

```
FROM:代表你新的镜像是来自于哪里,第一条必须是FROM;
MAINTAINER:镜像维护者姓名;
RUN:构建时执行命令,两种格式:
    shell格式 -> 命令行命令
    exec格式 -> docker 命令;
EXPOSE:当前容器对外暴露的端口;
WORKDIR:指定容器创建后,终端默认登录进来的工作目录;
ENV:用来定义构建镜像过程中构建环境变量;
VOLUME:容器数据卷,保存数据
ADD:将宿主机目录下的文件拷贝进镜像且会自动处理URL和解压tar压缩包
COPY:类似ADD,拷贝文件和目录到镜像;
CMD:指定容器启动后干啥,只有最后一个生效;
ENTRYPOINT:也是用来指定启动运行什么,不会被覆盖,后续的CDM相当于给自身传参;
```

## 6.2 自定义镜像Centos&java8

将最小版镜像,添加vim,ipconfig,java8的功能环境

jdk:https://mirrors.yangxingzhen.com/jdk/

```
FROM centos
MAINTAINER wyt<1114529308@qq.com>
 
ENV MYPATH /usr/local
WORKDIR $MYPATH
 
#安装vim编辑器
RUN yum -y install vim
#安装ifconfig命令查看网络IP
RUN yum -y install net-tools
#安装java8及lib库
RUN yum -y install glibc.i686
RUN mkdir /usr/local/java
#ADD 是相对路径jar,把jdk-8u171-linux-x64.tar.gz添加到容器中,安装包必须要和Dockerfile文件在同一位置
ADD jdk-8u191-linux-x64.tar.gz /usr/local/java/
#配置java环境变量
ENV JAVA_HOME /usr/local/java/jdk1.8.0_191
ENV JRE_HOME $JAVA_HOME/jre
ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib:$CLASSPATH
ENV PATH $JAVA_HOME/bin:$PATH
 
EXPOSE 80
 
CMD echo $MYPATH
CMD echo "success--------------ok"
CMD /bin/bash
```

构建 -> 有个点，当前目录

```
docker build -t 新镜像名字:tag .
```

![](images/WEBRESOURCEb5b85bc745156b81c23d3a778d215b35.octet-stream)

## 6.2虚悬镜像

```
#直接编译这玩意dockerfile随便写，符合语法就行
docker build -t  .
```

效果

![](images/WEBRESOURCE3e49b1be5e27c2c16a4a70c4166269e4.octet-stream)

这就是虚悬镜像。仓库名版本都是空的。这玩意会有隐藏风险，建议kill

```
#查看虚悬镜像
docker image ls -f dangling=true
#删除
docker image prune
```

# 7.DockerCompose

## 7.1 需求

- 之前运行一个镜像，需要添加大量的参数。

- 可以通过Docker-Compose编写这些参数。

- Docker-Compose可以帮助我们批量的管理容器。

- 只需要通过一个docker-compose.yml文件去维护即可。

## 7.2 下载安装

1. 去github官网搜索docker compose，下载1.24.1版本的Docker-Compose

```
https://github.com/docker/compose/releases/download/1.24.1/docker-compose-Linux-x86_64

```

1. 将下载好的文件，拖拽到Linux操作系统中

1. 需要将DockerCompose文件的名称修改一下，基于DockerCompose文件一个可执行的权限

```
mv docker-compose-Linux-x86_64 docker-compose 
chmod 777 docker-compose

```

1. 方便后期操作，配置一个环境变量

1. 将docker-compose文件移动到了/usr/local/bin，修改了/etc/profile文件，给/usr/local/bin配置到了PATH中

```
mv docker-compose /usr/local/bin 
vi /etc/profile
    export PATH=$JAVA_HOME:/usr/local/bin:$PATH 
source /etc/profile

```

1. 测试一下

在任意目录下输入docker-compose

## 7.3 Docker-Compose管理MySQL和Tomcat容器

```
version: '3.8' 
services: 
  mysql:                     # 服务的名称
    restart: always          # 代表只要Docker启动，那么这个容器就跟着一起启动
    image: daocloud.io/library/mysql:5.7.5-m15     # 指定镜像路径
    container_name: mysql    # 指定容器名称
    ports:
      - 3306:3306        # 指定端口号的映射
    environment:
      MYSQL_ROOT_PASSWORD: 123456         # 指定MySQL的ROOT用户登录密码
      TZ: Asia/Shanghai                 # 指定时区
    volumes:
      - /home/docker_mysql/:/var/lib/mysql        # 映射数据卷
  tomcat:
    restart: always          # 代表只要Docker启动，那么这个容器就跟着一起启动
    image: daocloud.io/library/tomcat:8.5.15-jre8     # 指定镜像路径
    container_name: tomcat    # 指定容器名称
    ports:
      - 8080:8080       
    environment:
      TZ: Asia/Shanghai                 
    volumes:
      - /home/tomcat_webapps:/usr/local/tomcat/webapps       
      - /home/tomcat_logs:/usr/local/tomcat/logs 
```

## 7.4 使用docker-compose命令管理容器

1.基于docker-compose.yml启动管理的容器

```
docker-compose up -d

```

2.关闭并删除容器

```
docker-compose down

```

3.开启关闭重启已经存在的由docker-compose维护的容器

```
docker-compose start|stop|restart

```

4.查看由docker-compose管理的容器

```
docker-compose ps

```

5.查看日志

```
docker-compose logs -f

```

## 7.5 docker-compose结合Dockerfile使用

> 
使用docker-compose.yml文件，以及Dockerfile文件在生成自定义镜像的同时启动当前镜像，并且由docker-compose去管理容器


```
# yml文件
version: '3.8'
services:
  web-demo:
    restart: always
    build:                           # 构建自定义镜像
      context: ../                   # 指定Dockerfile文件所在路径
      dockerfile: Dockerfile         # 指定Dockerfile文件名称
    image: demo:1.0
    container_name: demo
    ports:
      8081:8080
    environment:
      TZ: Asia/Shanghai

```

Dockerfile文件

```
from daocloud.io/library/tomcat:8.5.15-jre8
copy demo.war /usr/local/tomcat/webapps

```

