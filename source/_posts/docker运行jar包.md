---
title: docker运行jar包
abbrlink: 3803372939
date: 2023-04-07 19:42:19
tags: docker
---

# 1.编写 Dockerfile 文件

创建一个 Dockerfile 文件

> 文件名称必须是：Dockerfile

```dockerfile
# 首先指定jdk
FROM openjdk:8
# 版本、作者
LABEL version="1.0" maintainer="wrt"
# 指定临时文件目录为/tmp.在主机/var/lib/docker目录下创建了一个临时文件并链接到容器的/tmp
VOLUME /tmp
# 将jar包添加到容器中并更名为 wrt-chat.jar
COPY Spring-websocket.jar wrt-chat.jar
# 运行jar包
RUN bash -c 'touch /wrt-chat.jar'
# 定参
ENTRYPOINT ["java","-jar","/wrt-chat.jar"]
# 暴露8080端口
EXPOSE 8080
# 最后在终端构建：docker build -t rt-chat:v1 .
# 启动容器：docker run -d -p 8080:8080 rt-chat:v1
```

实际运行图如下：

输入`docker ps`，看到容器 UP 了，说明成功了~

> 不要忘记去防火墙开启 8080 端口~

![Alt text](1.png)

# 2. 修改项目后的操作

后期项目修改后，重新上传 jar 包，然后终端执行

```shell
# 先删除容器 docker rm [names]
# 最后在终端构建：docker build -t rt-chat:v1 .
# 启动容器：docker run -d -p 8080:8080 rt-chat:v1
```
