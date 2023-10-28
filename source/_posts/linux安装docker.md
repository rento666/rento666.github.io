---
title: linux安装docker
abbrlink: 3219586390
date: 2023-04-05 12:36:44
tags:
---

# Docker 的安装

以`OpenCloudOS 8.6`系统为例~

此系统是腾讯云的轻量级 Linux 发行版，基于 CentOS 8.6 构建。

## 1. 确定 Linux 版本

新版本的 Docker 对 Linux 系统版本有一定的要求。如果 Linux 的发行版系统是 centOS，安装最新版的 docker 需要 centOS 7 以上的系统。

在 Docker 安装帮助页面查看支持的系统版本。

Docker 帮助页面:https://docs.docker.com/engine/install/centos/

打开 终端，输入`cat /etc/os-release`查看系统信息。

确保自己的系统满足安装要求。

```shell
[root@VM-8-6-opencloudos ~]# cat /etc/os-release
NAME="OpenCloudOS"
VERSION="8.6"
ID="opencloudos"
ID_LIKE="rhel fedora"
VERSION_ID="8.6"
PLATFORM_ID="platform:oc8"
PRETTY_NAME="OpenCloudOS 8.6"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:opencloudos:opencloudos:8"
HOME_URL="https://www.opencloudos.org/"
BUG_REPORT_URL="https://bugs.opencloudos.tech/"
```

## 2.安装 Docker

官网提供的软件源速度比较慢，这里使用阿里云的软件源。

![Alt text](image.png)

- step 1: 安装必要的一些系统工具

```shell
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```

- Step 2: 添加软件源信息

```shell
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

- Step 3: 更新 yum 索引列表并安装 Docker 引擎

```shell
# centos7运行这个
sudo yum makecache fast
# centos8运行这个
sudo yum makecache
```

yum 索引列表没有更新可以直接使用 yum makecache

```shell
sudo yum install docker-ce
```

安装过程中会提示确认，输入 y 以确认。（应该是两次 y）

![Alt text](2.png)

- Step 4: 开启 Docker 服务

```shell
sudo service docker start
```

## 3.测试是否安装成功

在终端中输入`docker version`，安装成功会提示以下信息。

```text
[root@VM-8-6-opencloudos ~]# docker version
Client: Docker Engine - Community
 Version:           24.0.7
 API version:       1.43
 Go version:        go1.20.10
 Git commit:        afdd53b
 Built:             Thu Oct 26 09:09:18 2023
 OS/Arch:           linux/amd64
 Context:           default

Server: Docker Engine - Community
 Engine:
  Version:          24.0.7
  API version:      1.43 (minimum version 1.12)
  Go version:       go1.20.10
  Git commit:       311b9ff
  Built:            Thu Oct 26 09:08:20 2023
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.6.24
  GitCommit:        61f9fd88f79f081d64d6fa3bb1a0dc71ec870523
 runc:
  Version:          1.1.9
  GitCommit:        v1.1.9-0-gccaecfc
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
```

## 4.配置下载 docker 镜像的仓库

docker 的默认仓库 Docker Hub 下载速度在国内是很慢的，需要我们替换仓库。

这里我从网上找到了几个仓库：

[Docker 必备六大国内镜像](https://www.cnblogs.com/boonya/p/15954368.html)

这里我选择的是腾讯云的仓库：`https://mirror.ccs.tencentyun.com`

```shell
sudo mkdir -p /etc/docker

sudo tee /etc/docker/daemon.json <<-'EOF'
{
"registry-mirrors": ["https://mirror.ccs.tencentyun.com"]
}
EOF

sudo systemctl daemon-reload

sudo systemctl restart docker
```

## 5.测试 HelloWorld 镜像

在终端中输入`docker run hello-world`

![Alt text](3.png)

看到 Hello from Docker！ 字段说明我们的安装步骤没有问题，Docker 已经成功安装~
