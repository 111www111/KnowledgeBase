# 安装

1.清楚老版本

```
#列出MySQL相关的安装包
 rpm -qa | grep mysql
#依次删除安装包
 yum remove xxx

```

2.安装centos8下MySQL8的yum源

```
#下载源
wget https://dev.mysql.com/get/mysql80-community-release-el8-1.noarch.rpm
#安装源
rpm -ivh mysql80-community-release-el8-1.noarch.rpm

```

3.关闭默认的MySQL（一定要执行，不然安装时会提示找不到包）

```
 yum module disable mysql
```

4.安装MySQL

```
 yum install mysql-community-server --nogpgcheck
```

5.启动MySQL服务，查看运行状态

```
#启动mysql服务
systemctl start mysqld
#查看mysql运行状态
systemctl status mysqld

```

6.查找初始密码并登录修改密码

```
#查找初始密码 
grep 'temporary password' /var/log/mysqld.log
#通过初始密码登录
mysql -uroot -p
#初始密码状态
ALTER USER 'root'@'localhost' IDENTIFIED BY '111www111';
#降低密码等级和长度要求
set global validate_password.policy=LOW; 
set global validate_password.length=7;
#修改成自己的密码
ALTER USER 'root'@'localhost' IDENTIFIED BY '123@qwe';
#刷新权限
flush privileges;
#退出MySQL命令行
exit;
#bash下重启MySQL服务
systemctl restart mysqld;

```

7.远程登陆

```
#登录mysql
mysql -uroot -p123@qwe
#切换到mysql数据库
use mysql;
#让root可以通过任意IP登录
update user set host='%' where user='root';
exit;
#bash下重启MySQL服务
systemctl restart mysqld;

```

