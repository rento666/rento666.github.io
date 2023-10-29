---
title: mysql多版本共存
tags: MySQL
abbrlink: 2353769058
date: 2023-03-29 23:48:05
---

> 记录一下同时安装、运行 Mysql5.7 以及 8.0 版本。

# MySql 服务多版本同时运行

## 前提条件

- 下载 MySql8.0

我已经安装了一个 MySQL 版本（5.7），它占据了 3306 端口。

此时由于课程需要，我需要下载 8.0 版本，而我又不想卸载 5.7 版本，所以需要如下操作：

先下载 8.0 版本，官网链接：[https://dev.mysql.com/downloads/mysql/](https://dev.mysql.com/downloads/mysql/).

下载好后，把这个版本的（8.0）解压到同一个目录下，例如：
`D:\MySql\mysql-5.7.39-winx64`
`D:\MySql\mysql-8.0.32-winx64`

- 配置环境变量

复制 bin 目录的地址，新增到系统变量 path 中，这一步是为了到时能够直接在命令行中启动 mysql 服务，而不需要再次切换到 bin 目录下进行启动。

![](1.png)

![](2.png)

> 注意: 上图中，需要将 MySQL8.0 版本的路径**移动到**MySQL5.7 版本**之上**，也就是说，需要把新安装的版本的路径放到旧版本之上。
> 因为我这次安装的新版本是 8.0，所以 8.0 版本的路径要在 5.7 之上。（这关系到下面的初始化操作）

- 创建 my.ini 文件

在与 bin 目录平级下创建一个配置文件，名称为 my，后缀为 ini，如图：

![](3.png)

**my.ini**文件内容如下：

```
[mysql]
default-character-set=utf8
[mysqld]
port = 3307
basedir = D:\ProgrammingLanguage\Mysql8.0
datadir = D:\ProgrammingLanguage\Mysql8.0\data
max_connections=200
character-set-server=utf8
default-storage-engine=INNODB
sql-mode="STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION"
```

> 其中 **port** 为 mysql8.0 的端口号，因为 3306 被 5.7 占用，所以需要指定 8.0 的端口号.
> **basedir** 为 mysql8.0 的数据库路径，这个需要**根据自己的路径设置**！！
> **datadir** 为 mysql8.0 的数据文件路径，这个与**basedir**一样，都需要改成自己的路径。
> 其他的不需要修改。

- 停止 MySQL 服务

先将 5.7 的 MySQL 服务停掉，如下图(先别管图中的 MYSQL8)：

![](5.png)

- 生成 data 文件夹

解压后的 mysql-8.0.32-winx64 下面没有 data 文件夹，这个需要我们自己创建.

创建之前需要先到 8.0 的 bin 目录下，通过管理员身份运行 cmd 或者 power shell，执行`mysqld --version`，观察 mysql 版本是否为 8.0，若为 8.0，则可以执行下面的语句；若不是 8.0，则说明之前的设置为错误的，需要按步骤来设置。

创建 data 的命令如下：

`mysqld --initialize`

执行完上述命令后，会生成 data 文件夹，里面有诸多文件，我们目前只需要在乎里面一个.err 文件，里面存放着 mysql 的初始密码，例如：

![](4.png)

之后我们需要靠这个密码进行这个版本数据库的第一次登录，所以需要复制它。

- 创建 MySQL8 服务

继续在 8.0 的 bin 目录下，执行如下命令：

`mysqld install mysql8`

按下键盘的`win` + `R` 键，输入`regedit`，回车，进入注册表页面，路径为：`计算机\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services`，寻找有没有 `MySQL8`。

如果没有，则创建一个项，名为 MySQL8，然后根据已存在的 MySQL 进行新建具体内容。

如果有，则将`ImagePath`修改为`D:\ProgrammingLanguage\Mysql8.0\bin\mysqld.exe MySQL`。（这个路径按自己的路径来，我这里只是个例子）

> 特别注意： ImagePath 最后的 mysqld.exe MySQL 不能改动！！！

- 启动 MySQL 服务

以管理员身份打开 cmd 或者 power shell，执行`net start mysql` 、 `net start mysql8`，若两者均显示服务开启成功，则说明已经完成了九成九。

接下来我们来把 8.0 的 MySQL 重置密码。

- 修改初始密码

还记得我们之前复制的密码吗？

在 data 文件夹中，`.err` 为文件后缀名的文件，打开后复制初始的密码(详见“生成 data 文件夹”步骤)

之后在黑窗口执行如下命令：
`mysql -u root -P 3307 -p`
这时候会让我们输入密码，我们复制进去，回车，即可登录成功。

> 注意，一定要记得指定端口例如-P3307，不然到时启动的就是默认情况下(5.7)的 mysql 版本

依次执行如下命令:

```
# 修改加密规则
ALTER USER'root'@'localhost' IDENTIFIED BY 'root' PASSWORD EXPIRE NEVER;
# 更新一下用户的密码
ALTER USER'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'root';
# 刷新权限
FLUSH PRIVILEGES;
# 重置密码
alter user'root'@'localhost' identified by '你要设置的密码';
```

然后我们就大功告成了。

---

# 总结

`mysql -u 用户名 -P 端口号 -p 密码` 此命令来连接不同版本的数据库！
