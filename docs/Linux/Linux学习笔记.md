# Linux学习笔记

## 前置知识

* VMware：虚拟机，用来安装Linux系统。
* Linux系统：比如Ubuntu、CentOS。
* xshell：通过ssh连接到Linux系统
* vscode：远程连接Linux系统

## 开发环境配置

### 安装VMware

### 安装Ubuntu



### vscode远程连接Linux

* 手动启动Openssh服务

```bash
sudo apt update
sudo apt install openssh-server
sudo systemctl start ssh
sudo systemctl enable ssh
```

检查是否开启ssh服务

```bash
sudo systemctl status ssh
```

* vscode端安装open-ssh插件

* 远程连接

  ![image-20250919101426186](https://xubenshan-pic.oss-cn-beijing.aliyuncs.com/img/image-20250919101426186.png)

  ![image-20250919101443983](https://xubenshan-pic.oss-cn-beijing.aliyuncs.com/img/image-20250919101443983.png)

  输入用户名@host。host指的Linux服务器的IP地址。可以通过`ifconfig`查看。

## 常见操作

### 如何安装软件

在官网下载的软件安装包可能是AppImage类型：

以屏幕截图软件snipaste为例；

```bash
//首先进入安装包所在目录
cd /home/xbs/Downloads/
//添加可执行权限
sudo chmod +x Snipaste-2.10.8-x86_64.AppImage
//直接运行
./Snipaste-2.10.8-x86_64.AppImage

```



​                                                                                             