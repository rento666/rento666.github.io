---
title: docker安装mysql
abbrlink: 632199233
date: 2023-04-06 15:35:20
tags: docker
---

> 在学习了上一节[linux 安装 docker](https://rento666.github.io/posts/3219586390)后，这一节我们来学习如何在 docker 中安装 MySQL
> 示例中：MySQL 版本为 5.7

# 1. 检查当前所有 docker 下载的镜像

```shell
docker images
```

如下图所示，我们在上一节安装了一个`hello-world`镜像：

![Alt text](1.png)

# 2. 在 docker 仓库中搜索 mysql 的镜像：

```shell
 docker search mysql
```

下载镜像：

```shell
docker pull mysql
```

**先别下载！**，因为它默认是下载最新版本，所以我们这里指定版本 5.7，即为`docker pull mysql:5.7`

操作如下所示：
![Alt text](2.png)

# 3.查看是否下载成功

```shell
docker images
```

如图所示，有 MySQL5.7 即为下载成功~
![Alt text](3.png)

# 4.启动 mysql 容器

```shell
docker run -p 3306:3306 --name mysql -v /mydata/mysql/log:/var/log/mysql -v /mydata/mysql/data:/var/lib/mysql -v /mydata/mysql/conf:/etc/mysql -e MYSQL_ROOT_PASSWORD=root123 -d mysql:5.7
```

参数说明：

- `-p 3306:3306`：将容器的 3306 端口映射到本地的 3306 端口
- `--name mysql`：容器名称
- `-v /mydata/mysql/log:/var/log/mysql`：将容器的日志文件夹挂载到本地
- `-v /mydata/mysql/data:/var/lib/mysql`：将容器的配置文件夹挂载到本地
- `-v /mydata/mysql/conf:/etc/mysql`：将容器的配置文件夹挂载到本地
- `-e MYSQL_ROOT_PASSWORD=root123`：初始化 root 用户的密码
- `-d mysql:5.7`：指定镜像

# 5.查看是否启动成功

```shell
docker ps
```

发现是 Exited 状态，也就是启动失败了：
![Alt text](4.png)

输入`docker log mysql` 查看一下日志
![Alt text](5.png)

## 解决办法

在查阅一番资料后，有了解决办法：

```shell
# 宿主机创建数据存放目录映射到容器
mkdir -p /mydata/mysql/data

# 宿主机创建配置文件目录映射到容器
mkdir -p /mydata/mysql/conf #(需要在此目录下创建"conf.d"、"mysql.conf.d"两个目录)
mkdir -p /mydata/mysql/conf/conf.d # (建议在此目录创建my.cnf文件并进行相关MySQL配置)
mkdir -p /mydata/mysql/conf/mysql.conf.d

# 宿主机创建日志目录映射到容器
mkdir -p /mydata/mysql/log

# 创建my.cnf
touch /mydata/mysql/conf/conf.d/my.cnf

# 然后vim
vim /mydata/mysql/conf/conf.d/my.cnf

# 进入到vim后，按i进入编辑模式

# 把下面代码复制进去-------------------------------
# 注意，这里必须是0.0.0.0，不能是127.0.0.1，否则在自己电脑(windows/mac)上连不上服务器(linux docker)上的MySQL
[mysqld]
bind-address = 0.0.0.0
port = 3306
user = mysql
default-storage-engine = InnoDB
sql_mode = ""
max_connections = 100
character-set-server = utf8mb4
collation-server = utf8mb4_general_ci
# 把上面代码复制进去-------------------------------

# 然后Esc退出编辑模式，输入:wq保存退出
```

然后，重新启动 MySQL 容器，应该可以正常启动了。

```shell
docker restart mysql
```

现在再来进行 `docker ps`，可以看到运行成功了：
![Alt text](6.png)

# 6.进入容器

```shell
docker exec -it mysql bash
```

> **注意**，上面的 mysql 替换成你自己的容器名，在图 6 中，容器名为 NAMUES 下的`mysql`

# 7.登录 MySQL

```shell
mysql -u root -p root123
```

> 这里的密码是我们创建容器的时候设置的，在第 4 步，忘记的可以去看第 4 步那个密码。

## 创建用户并开启远程登录

- 方法 1：给子用户开启所有数据库权限

下面是创建用户并开启远程登录的命令：（请注意，如果你不想这个账号对所有的数据库都有权限，可以用另一种方法）

```shell
# 创建test账号，密码为test123
CREATE USER 'test'@'%' IDENTIFIED BY 'test123';
# 赋予test用户对所有数据库的所有权限
GRANT ALL PRIVILEGES ON *.* TO 'test'@'%';
# 刷新权限
FLUSH PRIVILEGES;
```

- 方法 2：给子用户开启部分数据库权限

```shell
# 创建test账号，密码为test123
CREATE USER 'test'@'%' IDENTIFIED BY 'test123';
# 创建sakila数据库并设置utf8mb4字符集
CREATE DATABASE IF NOT EXISTS sakila DEFAULT CHARSET utf8mb4 COLLATE utf8mb4_general_ci;
# 赋予test用户对sakila数据库的所有权限
GRANT ALL PRIVILEGES ON \`sakila\`.* TO 'test'@'%';
# 刷新权限
FLUSH PRIVILEGES;
```

# 8.退出容器

```shell
exit
```

# 9.docker 常用命令

```shell
# 删除容器(参数可以是容器名称或容器ID)
docker rm mysql
# 删除镜像
docker rmi mysql
# 启动容器
docker start mysql
# 启动容器/重启容器
docker restart mysql
# 停止容器
docker stop mysql
# 进入容器
docker exec -it mysql bash
```

# 11.dockerfile

将上述正确的步骤总结为 dockerfile：

**TODO**

```dockerfile
FROM mysql:5.7
```
