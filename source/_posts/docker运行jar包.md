---
title: docker运行jar包
date: 2023-10-28 19:42:19
tags:
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

![Alt text](image-1.png)
