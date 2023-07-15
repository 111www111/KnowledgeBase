Azkaban应用

## Azkaban简介

### 前言

1. 在简单的java-web系统中需要定期定时的执行一些任务

1. 在大数据分析系统中，会存在以下特点

- 一个完整的数据分析系统往往由多个任务组成，比如java程序，shell脚本程序，MR程序，hive脚本，Flink程序，spark程序等

- 各任务之间存在先后顺序

1. 基于以上两点，无论是在java-web系统中，还是在大数据分析系统中，都需要一个任务调度器

在java系统中可以通过quartz完成；在大数据分析系统中可以通过Crontab、Oozie、Azkaban、 Airflow等完成

### 什么是Azkaban

Azkaban是由Linkedin公司推出的一个批量工作流任务调度器。Azkaban可以通过简单的配置完成任务之间的依赖关系（依赖关系必须是有向无环图（DAG））

![](images/WEBRESOURCE2d7a9b3672d649965a0605bc3f9efdb6.octet-stream)

### 为什么选择Azkaban

Azkaban有以下特征：

![](images/WEBRESOURCEc40d45824a953335726f4b6c60867ac4.octet-stream)

除此之外，Azkaban是通过java开发的，通过gradle构建项目，方便二次开发

### Azkaban架构

![](images/WEBRESOURCE4ac46ba8a8d485c3228a04da6ee50e9a.octet-stream)

- AzkabanWebServer-是所有Azkaban的主要管理器。它处理项目管理，身份验证，调度程序和执行监视。 它还提供Web用户界面。 使得Azkaban的使用变得很简单。

> The AzkabanWebServer is the main manager to all of Azkaban. It handles project management, authentication, scheduler, and monitoring of executions. It also serves as the web user interface.Using Azkaban is easy. Azkaban uses *.job key-value property files to define individual tasks in a work flow, and the dependencies property to define the dependency chain of the jobs. These job files and associated code can be archived into a *.zip and uploaded through the web server through the Azkaban UI or through curl.

- AzkabanExecutorServer-是azkaban的执行者。

以前的Azkaban版本(version 3.0之前)在单个服务器中同时具有AzkabanWebServer和 AzkabanExecutorServer功能。此后，执行程序已被分离到单独的服务器中。拆分这些服务的原因有很多：方便扩展Executor的数量，并在失败的情况下可以恢复。分离以后在对Azkaban升级的时候对用户的使用影响很小。

> Previous versions of Azkaban had both the AzkabanWebServer and the AzkabanExecutorServer features in a single server. The Executor has since been separated into its own server. There were several reasons for splitting these services: we will soon be able to scale the number of executions and fall back on operating Executors if one fails. Also, we are able to roll our upgrades of Azkaban with minimal impact on the users. As Azkaban's usage grew, we found that upgrading Azkaban became increasingly more difficult as all times of the day became 'peak'.

- MySQL-用来存储Azkaban的数据信息。在AzkabanWebServer以及AzkabanExecutorServer中都在使用

> How does AzkabanWebServer use the DB?The web server uses the db for the following reasons:Project Management - The projects, the permissions on the projects as well as the uploaded files.Executing Flow State - Keep track of executing flows and which Executor is running them.Previous Flow/Jobs - Search through previous executions of jobs and flows as well as access their log files.Scheduler - Keeps the state of the scheduled jobs.SLA - Keeps all the sla rulesHow does the AzkabanExecutorServer use the DB?The executor server uses the db for the following reasons:Access the project - Retrieves project files from the db.Executing Flows/Jobs - Retrieves and updates data for flows and that are executingLogs - Stores the output logs for jobs and flows into the db.Interflow dependency - If a flow is running on a different executor, it will take state from the DB.There is no reason why MySQL was chosen except that it is a widely used DB. We are looking to implement compatibility with other DB's, although the search requirement on historically running jobs benefits from a relational data store.

```
相关网址：
azkaban官网：https://azkaban.github.io/
azkaban帮助文档：https://azkaban.github.io/azkaban/docs/latest/
```

## 源码编译

1. 安装git

```
yum install -y git
```

1. 通过git命令克隆azkaban

```
git clone https://github.com/azkaban/azkaban.git
```

1. 编译

```

a.进入到azkaban目录下
cd azkaban
b.通过执行gradlew编译
 ./gradlew build installDist
```

1. 编译之后生成的压缩文件在build/distributions目录下

## 安装

> In version 3.0 we provide three modes: the stand alone "solo-server" mode, the heavier weight two server mode and distributed multiple-executor mode.

### solo-server-主要应用在测试环境以及少量的任务调度

> In solo server mode, the DB is embedded H2 and both web server and executor server run in the same process. This should be useful if one just wants to try things out. It can also be used on small scale use cases.

安装前必须保证JDK已经安装成功并且配置了环境变量

1. 传输安装文件到Linux系统

1. 解压安装文件

```

[root@CentOSB usr]# pwd
/usr
[root@CentOSB usr]# ls
azkaban-solo-server-3.81.0-1-g304593d.tar.gz  etc    include  lib64    local  share  tmp
bin                                           games  lib      libexec  sbin   src
[root@CentOSB usr]# tar -zxf azkaban-solo-server-3.81.0-1-g304593d.tar.gz 
[root@CentOSB usr]# ls
azkaban-solo-server-3.81.0-1-g304593d         bin  games    lib    libexec  sbin   src
azkaban-solo-server-3.81.0-1-g304593d.tar.gz  etc  include  lib64  local    share  tmp
[root@CentOSB usr]# mv azkaban-solo-server-3.81.0-1-g304593d azkaban-solo-server
```

1. 目录结构

```

[root@CentOSB azkaban-solo-server]# pwd
/usr/azkaban-solo-server
[root@CentOSB azkaban-solo-server]# ll
总用量 8
drwxr-xr-x. 3 root root   67 12月 17 16:11 bin      #启动脚本
drwxr-xr-x. 2 root root   82 12月 17 16:11 conf     #配置目录
drwxr-xr-x. 2 root root 4096 12月 17 16:11 lib      #运行所需依赖jar
drwxr-xr-x. 3 root root   22 12月 17 16:11 plugins  #插件安装目录
drwxr-xr-x. 2 root root 4096 12月 17 16:11 sql      #sql脚本
drwxr-xr-x. 6 root root   73 12月 17 16:11 web      #web服务相关
```

```

[root@CentOSB conf]# pwd
/usr/azkaban-solo-server/conf
[root@CentOSB conf]# ll
总用量 8
-rw-r--r--. 1 root root 1873 11月 19 2019 azkaban.properties
-rw-r--r--. 1 root root  267 11月 19 2019 azkaban-users.xml
-rw-r--r--. 1 root root    0 11月 19 2019 global.properties
```

```

[root@CentOSB jobtypes]# pwd
/usr/azkaban-solo-server/plugins/jobtypes
[root@CentOSB jobtypes]# ll
总用量 4
-rw-r--r--. 1 root root 43 11月 19 2019 commonprivate.properties
```

1. 修改azkaban.properties配置文件

更改两个key对应的值

```

default.timezone.id=Asia/Shanghai #修改时区
jetty.port=8082 #配置azkabanweb服务器地址
```

1. 修改commonprivate.properties配置文件

添加以下key=value

```

memCheck.enabled=false #关闭执行节点内存检查，默认如果执行节点内存小于6GB，不会提交任务
```

1. 启动solo-server

执行bin目录下的启动文件

```

[root@CentOSB bin]# pwd
/usr/azkaban-solo-server        #特别提醒，一定在这个目录下执行启动文件，不能进入到bin目录
[root@CentOSB bin]# ./bin/start-solo.sh
```

   启动azkaban不能访问的时候，就应该到日志文件中查看启动日志信息

   

![](images/WEBRESOURCEbe6fe2e817770700ac1c5ce15ddfe223.octet-stream)

1. 验证

- 通过jps

```

[root@CentOSB azkaban-solo-server]# jps
7394 AzkabanSingleServer
7414 Jps
```

- 通过浏览器

![](images/WEBRESOURCEad1052cd944504615100aa657bb4c5c3.octet-stream)

输入用户名和密码分别为azkaban、azkaban进入到azkaban提供的UI界面

1. 执行关闭文件停止azkaban



```

[root@CentOSB azkaban-solo-server]# pwd
/usr/azkaban-solo-server
[root@CentOSB azkaban-solo-server]# ./bin/shutdown-solo.sh 
Killing solo-server. [pid: 7394], attempt: 1
shutdown succeeded
```

### two server mode and distributed multiple-executor mode-主要应用在生产环境

> The two server mode is for more serious production environment. Its DB should be backed by MySQL instances with master-slave set up. The web server and executor server should run in different processes so that upgrading and maintenance shouldn't affect users.The multiple executor mode is for most serious production environment. Its DB should be backed by MySQL instances with master-slave set up. The web server and executor servers should ideally run in different hosts so that upgrading and maintenance shouldn't affect users. This multiple host setup brings in robust and scalable aspect to Azkaban.

安装前必须保证JDK已经安装成功并且配置了环境变量

以三个节点为例完成安装

1. mysql数据库相关准备工作

- mysql主从复制安装完成

- 把创建表的脚本传输到Linux系统（mysql的主机）中

- 登录到mysql；通过source执行创建表的脚本，创建azkaban运行需要的数据库表

(也可以通过Navicat图形界面运行脚本创建mysql数据库里面的表)

```

mysql> use baizhi;
Database changed
mysql> source /tmp/create-all-sql-3.81.0-1-g304593d.sql
mysql> show tables
    -> ;
+-----------------------------+
| Tables_in_baizhi            |
+-----------------------------+
| QRTZ_BLOB_TRIGGERS          |
| QRTZ_CALENDARS              |
| QRTZ_CRON_TRIGGERS          |
| QRTZ_FIRED_TRIGGERS         |
| QRTZ_JOB_DETAILS            |
| QRTZ_LOCKS                  |
| QRTZ_PAUSED_TRIGGER_GRPS    |
| QRTZ_SCHEDULER_STATE        |
| QRTZ_SIMPLE_TRIGGERS        |
| QRTZ_SIMPROP_TRIGGERS       |
| QRTZ_TRIGGERS               |
| active_executing_flows      |
| active_sla                  |
| execution_dependencies      |
| execution_flows             |
| executor_events             |
| executors                   |
| project_events              |
| project_files               |
| project_flow_files          |
| project_flows               |
| project_permissions         |
| project_properties          |
| project_versions            |
| projects                    |
| properties                  |
| ramp                        |
| ramp_dependency             |
| ramp_exceptional_flow_items |
| ramp_exceptional_job_items  |
| ramp_items                  |
| triggers                    |
+-----------------------------+
34 rows in set (0.00 sec)
```

1. 时钟同步



```
1.安装ntp
[root@flink ~]# yum install -y ntp
​
2.同步时钟
[root@flink ~]# ntpdate cn.pool.ntp.org
20 May 22:58:49 ntpdate[27151]: step time server 111.206.66.39 offset 34470.795827 sec
​
3.把当前系统时间设置为硬件时间
[root@flink ~]# clock -w
```

1. 安装Executor-Server（在三个节点中完成）

- 把压缩文件传输到三个节点中

- 分别做解压操作

```

[root@CentOSA usr]# pwd
/usr
[root@CentOSA usr]# ls
azkaban-exec-server-3.81.0-1-g304593d.tar.gz  etc    include  lib    libexec  nginx  share  tmp
bin                                           games  java     lib64  local    sbin   src
[root@CentOSA usr]# tar -zxf azkaban-exec-server-3.81.0-1-g304593d.tar.gz 
[root@CentOSA usr]# mv azkaban-exec-server-3.81.0-1-g304593d azkaban-exec-server
```

- 编辑azkaban.properties配置文件

修改以下key的对应值

```

default.timezone.id=Asia/Shanghai
executor.port=12321
jetty.port=8082
azkaban.webserver.url=http://192.168.77.201:8082
mysql.host=192.168.77.201
mysql.database=baizhi
mysql.user=root
mysql.password=123456
​
-- mysql 8 +  记得换lib
mysql.database=azkaban?useSSL=false&serverTimezone=UTC&characterEncoding=utf-8
```

- 编辑commonprivate.properties配置文件

添加以下key=value

```
memCheck.enabled=false
```

- 启动azkaban执行服务器并验证

```
[root@CentOSA azkaban-exec-server]# pwd
/usr/azkaban-exec-server
[root@CentOSA azkaban-exec-server]# ./bin/start-exec.sh 
[root@CentOSA azkaban-exec-server]# jps
25666 Jps
25653 AzkabanExecutorServer
```

- 激活azkaban执行服务器(每次重启，都需要激活)

```
[root@CentOSB azkaban-exec-server]# pwd
/usr/azkaban-exec-server
[root@CentOSB azkaban-exec-server]# curl -G "localhost:$(<./executor.port)/executor?action=activate" && echo
​
{"status":"success"}   #执行之后的结果
```

1. 安装azkaban的webServer(在一个节点中安装)

- 传输安装文件

- 解压

```
[root@CentOSA usr]# pwd
/usr
[root@CentOSA usr]# tar -zxf azkaban-web-server-3.81.0-1-g304593d.tar.gz 
[root@CentOSA usr]# mv azkaban-web-server-3.81.0-1-g304593d azkaban-web-server
```

- 编辑azkaban.properties配置文件

修改以下key为对应的value

```

default.timezone.id=Asia/Shanghai
jetty.port=8082
mysql.host=192.168.77.201
mysql.database=baizhi
mysql.user=root
mysql.password=123456
azkaban.executorselector.filters=StaticRemainingFlowSize,CpuStatus #关闭对执行服务器内存检查-测试环境
```

- 启动webServer

```

[root@CentOSA azkaban-web-server]# pwd
/usr/azkaban-web-server
[root@CentOSA azkaban-web-server]# ./bin/start-web.sh
```

- 验证

- jps命令完成

- 浏览器访问

## 数据库表创建失败的解决方案

把azkaban运行需要的数据库表创建出来

在mysql5.6以及之前的版本，索引字段长度不能超过767bytes（对应到数据库列类型varchar，不能超过varchar(255)）;

```

1071 - Specified key was too long; max key length is 767 bytes
```



解决办法

- 使用mysql5.7以及以后的版本

- 可以通过前缀索引的方式创建索引：取字段的前一部分作为索引

```

CREATE TABLE execution_jobs (
  exec_id       INT          NOT NULL,
  project_id    INT          NOT NULL,
  version       INT          NOT NULL,
  flow_id       VARCHAR(128) NOT NULL,
  job_id        VARCHAR(512) NOT NULL, -- 这里可以是512没有问题
  attempt       INT,
  start_time    BIGINT,
  end_time      BIGINT,
  status        TINYINT,
  input_params  LONGBLOB,
  output_params LONGBLOB,
  attachments   LONGBLOB,
  PRIMARY KEY (exec_id, job_id(255), flow_id, attempt) -- job_id(255)就是前缀索引
);
​
CREATE INDEX ex_job_id
  ON execution_jobs (project_id, job_id(255));
```



## Azkaban工作流

### 快速入门

1. Flow1.0版本开发（很少使用，通过.job配置工作流的）



- 在本地磁盘的某一个目录下创建文件夹

![](images/WEBRESOURCE71c867587b6f4f1fbe0b2c5f744e8849.octet-stream)

- 进入到创建的文件夹里面，创建以.job结尾的文件（Azkaban1.0版本的flow是.job文件）

![](images/WEBRESOURCEc52115994d64af6c0c8b844a65016c13.octet-stream)



- 通过notepad++打开文件，添加以下内容

```
type=command
command=echo 'hello,azkaban'
```

- 把job文件打成zip压缩包

![](images/WEBRESOURCE78b54403e6c4f12750ef14957add7a75.octet-stream)

- 进入Azkaban的Web界面，创建新project

![](images/WEBRESOURCE69c21fd17df523bf714fc829f9e09979.octet-stream)



- 把zip文件上传到Azkaban

![](images/WEBRESOURCE6a929fcce02c33b4aabe7a922bc62f6a.octet-stream)



- 执行工作流

![](images/WEBRESOURCE3c6e3eb7183a2b7d2c709d934d012066.octet-stream)

时间规则通过cron表达式完成：秒 分 时 日 月 星期 [年]



1. Flow2.0版本开发（通过.flow配置工作流）

- 创建一个文件夹

- 在文件夹中创建.project结尾的文件

- 在文件中添加内容azkaban-flow-version: 2.0

- 创建.flow结尾的文件

- 在文件中添加以下内容

```
nodes:
  - name: jobA
    type: command
    config:
      command: echo "This is an echoed text."
```

- 把以上两个文件打包成zip压缩包

- 上传到Azkaban-Web界面

### 执行shell脚本

- 创建文件夹demo3

- 在文件夹创建.project结尾的文件

- 在文件中添加内容azkaban-flow-version: 2.0

- 创建.flow结尾的文件

- 在文件中添加以下内容

```
nodes:
  - name: jobA
    type: command
    config:
      command: sh ./demo3/bin/showpath.sh
```

- 创建bin目录，并且在里面创建showpath.sh文件

- 打开文件添加一下内容

```

current_path=$(pwd)
echo ===============current_path==============
echo $current_path
echo ===============tree==============
tree $current_path
```

- 将demo3打成zip压缩文件，传输到azkban执行

### 调用java代码

- idea开发工具创建maven项目，创建启动类

```

package com.baizhi.azkaban;
​
public class SpringBootApplication {
    public static void main(String[] args) {
        System.out.println("this is azkaban demo");
    }
}
```

- 通过maven把项目打包

- 创建文件夹demo4

- 在里面编写.project文件

```
azkaban-flow-version: 2.0
```

- 在文件夹里面编写.flow文件

```
nodes:
  - name: jobA
    type: javaprocess
    config:
        classpath: ./demo4/lib/*
        java.class: com.baizhi.azkaban.SpringBootApplication



```

- 文件夹中创建lib目录，并把jar包复制到lib目录下

- 将demo4打成zip压缩包，传输到azkaban

### 多job执行

1. 多个无关job



demo02.flow文件中的内容如下

```

nodes:
  - name: jobA
    type: javaprocess
    config:
        classpath: ./demo5/lib/*
        java.class: com.baizhi.azkaban.SpringBootApplication
    
  - name: jobB
    type: command
    config:
      command: sh ./demo5/bin/showpath.sh
```

1. 顺序执行

```
nodes:
  - name: jobC
    type: noop
    dependsOn:
     - jobA
     - jobB
  - name: jobA
    type: javaprocess
    config:
        classpath: ./demo6/lib/*
        java.class: com.baizhi.azkaban.SpringBootApplication
  - name: jobB
    type: command
    config:
        command: sh ./demo6/bin/showpath.sh
  - name: jobD
    type: command
    dependsOn:
     - jobC
    config:
        command: echo 'jobA,jobB,jobC is over'



```

![](images/WEBRESOURCEff21bae96b59a446102b99bce8306646.octet-stream)

1. 内嵌工作流

```
nodes:
  - name: jobC
    type: noop
    dependsOn:
     - jobA
     - jobB
     - jobE
  - name: jobA
    type: javaprocess
    config:
        classpath: ./demo7/lib/*
        java.class: com.baizhi.azkaban.SpringBootApplication
  - name: jobB
    type: command
    config:
        command: sh ./demo7/bin/showpath.sh
  - name: jobD
    type: command
    dependsOn:
     - jobC
    config:
        command: echo 'jobA,jobB,jobC is over'
  - name: jobE
    type: flow
    nodes:
     - name: jobE1
       type: command
       config: 
           command: echo 'jobE1'
     - name: jobE2
       type: command
       config: 
           command: echo 'jobE2'



```



![](images/WEBRESOURCE57c2d872af17761387806d24b89150d2.octet-stream)

### 配置Email

第一种配置方式，在配置文件中配置，所有的job都使用这一套配置

在executorServer以及webServer的配置文件添加以下配置

```

# email 相关配置
mail.sender=1114529308@qq.com
mail.host=smtp.qq.com
mail.user=1114529308@qq.com
mail.password=ttzgrbxfqhvybifc  # 邮箱的授权码|密码
# 任务调度成功&失败的发送邮箱
job.failure.email=1114529308@qq.com #当job执行失败时，接收信息的邮箱
job.success.email=1114529308@qq.com #当job执行成功时，接收信息的邮箱
```



第二种配置方式：为每一个flow配置单点的邮件；在flow文件里面配置的

也可以为每一个flow配置单独的邮箱

```

---
config:
 user.to.proxy: root #代理用户
 failure.emails: 1114529308@qq.com,xx@qq.com #当job执行失败时，接收信息的邮箱
 success.emails: 1114529308@qq.com,xx@qq.com #当job执行成功时，接收信息的邮箱
 notify.emails: 1114529308@qq.com,xx@qq.com #成功与失败都会接收信息的邮箱
nodes:
 - name: job01
   type: command
   config:
     command: sh ./demo08/bin/hdfs.sh
```



# 启停脚本设计：

```
#!/bin/bash
start-web(){
	for i in hadoop101;do
		ssh $i "cd /software/azkaban/web/web;bin/start-web.sh"
	done
}
stop-web(){
	for i in hadoop101;do
		ssh $i "cd /software/azkaban/web/web;bin/shutdown-web.sh"
	done
}

start-exec(){
	for i in hadoop101 hadoop102 hadoop103;do
		ssh $i "cd /software/azkaban/exec/exec;bin/start-exec.sh"
	done
}
activate-exec(){
	for i in hadoop101 hadoop102 hadoop103;do
		ssh $i "cd /software/azkaban/exec/exec;curl -G "$i:12321/executor?action=activate" && echo"
	done
}
stop-exec(){
	for i in hadoop101 hadoop102 hadoop103;do
		ssh $i "cd /software/azkaban/exec/exec;bin/shutdown-exec.sh"
	done
}

case $1 in
	start-exec)
		start-exec
	;;
	activate-exec)
		activate-exec
	;;
	stop-exec)
		stop-exec
	;;
	stop-web)
		stop-web
	;;
	start-web)
		start-web
	;;
esac
```

