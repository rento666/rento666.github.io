---
title: docker快速入门
tags: docker
abbrlink: 4099767755
date: 2023-03-28 18:21:12
---

---

> 参考资料：[Docker 快速入门](https://docker.easydoc.net/)

---

# 安装 docker

- 安装 docker desktop

> 系统版本：win11 家庭版（2023-03-28 最新版）

我这里安装的是 Windows 版本的 docker，这是链接:
[https://www.docker.com/products/docker-desktop/](https://www.docker.com/products/docker-desktop/)
有时候打不开这个网站，所以我把文件传到了百度网盘上，这是链接：
[链接：https://pan.baidu.com/s/1onewNBdGbkT37VLfMhADxg 提取码：rent](https://pan.baidu.com/s/1onewNBdGbkT37VLfMhADxg).

这里先安装[Docker Desktop Installer.exe]，另一个文件稍后会用到。

默认下一步直到安装完成，然后打开[控制面板]、点击[程序]、点击[启用或关闭 Windows 功能]、将[Windows 虚拟机监控程序平台]和[适用于 Linux 的 Windows 子系统]打勾，然后点击确定，等待安装完成，之后根据提示重启电脑。

打开 docker desktop，可能有报错，我这里提示这个：

```
Installing, this may take a few minutes… WslRegisterDistribution
failed with error: 0x800701bc Error: 0x800701bc WSL 2 ???
https://aka.ms/wsl2kernel

Press any key to continue…
```

- 更新 wsl2

这时候安装我们百度网盘中的第二个文件[wsl_update_x64.msi]，（这个文件也可以在微软官方下载，链接：[https://learn.microsoft.com/zh-cn/windows/wsl/install-manual#step-4---download-the-linux-kernel-update-package](https://learn.microsoft.com/zh-cn/windows/wsl/install-manual#step-4---download-the-linux-kernel-update-package)）。安装完成之后再打开 docker desktop，应该就不会报错了。

- 管理员身份运行命令

然后**以管理员身份**运行 cmd(PowerShell)黑窗口，运行如下命令，将 WSL 2 设置为默认版本：

```
wsl --set-default-version 2
```

- 安装 Linux 系统

方式一：命令行安装 Linux 内核 `wsl.exe --install -d Ubuntu`

方式二：打开 Microsoft Store，搜索 Ubuntu，安装。（也可以选择 Centos 等版本，这里我安装的是 Ubuntu）。

> 上面命令很可能你安装不了，微软商店你也可能打不开，如果遇到这个问题，参考：
> https://blog.csdn.net/qq_42220935/article/details/104714114

首次启动新安装的 Linux 分发版时，将打开一个控制台窗口，系统会要求你等待一分钟或两分钟，以便文件解压缩并存储到电脑上。 未来的所有启动时间应不到一秒。

然后，需要为新的 Linux 分发版创建用户帐户和密码。

![](ubuntuinstall.png)

**输入密码时不会显示输入的个数！！！**

- 设置开机启动 Hypervisor

`bcdedit /set hypervisorlaunchtype auto`

> 注意要用管理员权限打开 PowerShell

- 确保 BIOS 已开启虚拟化

可通过任务管理器查看是否已启用虚拟化：
![](kvaf7ody.png)

- 修改镜像加速源

`"registry-mirrors": ["https://registry.docker-cn.com"]`

打开 docker desktop，打开设置，选择 Docker Engine，在文本框中新增上述代码，如下图：

![](l25jdwrn.png)

# Docker 安装 Redis

Redis 官网：[https://redis.io/](https://redis.io/)

> 官网下载安装教程只有源码安装方式，没有 Windows 版本。想要自己安装 windows 版本需要去找别人编译好的安装包。

Docker 官方镜像仓库查找 Redis ：[https://hub.docker.com/](https://hub.docker.com/)

一个命令跑起来：`docker run -d -p 6379:6379 --name redis redis:5.0`

在 PowerShell 运行这个命令，就会去镜像仓库获取 redis。

`redis:latest` 表示 redis 最新版。**一般生产环境不用最新版，最好指定一个版本，如 redis5.0 版本**

- 命令解释

|    命令    |                   解释                   |
| :--------: | :--------------------------------------: |
| docker run |          docker 运行一个“软件”           |
|     -d     |                 后台运行                 |
|     -p     | 端口暴露，把容器里边的 6379 暴露到宿主机 |
|   --name   |             给容器命名 redis             |
| redis:5.0  |      这个软件源（redis，5.0 版本）       |

`docker run` 是 docker 运行一个“软件”；`-d` 表示后台运行

命令参考：[https://docs.docker.com/engine/reference/commandline/run/](https://docs.docker.com/engine/reference/commandline/run/)
