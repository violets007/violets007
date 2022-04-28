---
# date是手动设置此篇文章编写的时间
date: "2022/04/28 08:08"

# 手动设置此篇文章封面
coverUrl: "docker-bds/install-docker-on-linux.png"

# 是否置顶
sticky: false

# 设置keyword 多个以,分开
keyword: bds,docker

# 设置description
description: docker运行LiteLoaderBDS教程

# 手动设置标题，否则使用h1标签作为标题
title: docker运行LiteLoaderBDS

# 这是设置标签，数组形式
tag: [docker,bds,LiteLoaderBDS]

# 这里设置类别，数组形式
categories: [docker,bds,LiteLoaderBDS]
---



# 手把手教你在Ubuntu上搭建Minecraft(基岩版)服务器

## 什么是BDS？
> Bedrock Dedicated Server，直译为基岩版专用服务器（惯用简称BDS），是由微软开发的 Minecraft: Bedrock Edition （我的世界基岩版）专用服务端，用于搭建 MCBE 服务器。
微软（Mojang）官方提供了 Windows 与 Ubuntu 两个版本的服务端文件。结合众多服主的开服经验，推荐优化更多的Windows版服务端。

## 为什么使用LiteLoaderBDS搭建BDS
> 因为可扩展性更高，提供高度自定义的api，可自由安装插件和自行编写插件，支持热更新插件，以及更好的社区环境，具体可参考[LiteLoaderBDS](https://github.com/LiteLDev/LiteLoaderBDS/blob/main/README_zh-cn.md) : 。

## 环境准备
- Ubuntu 18.04

  #### 1.查看系统版本

  ```bash
  cat /proc/version
  ```

- Docker

  #### 1.首先，更新现有的软件包列表：

  ```bash
  sudo apt update
  ```

  #### 2.将 Docker 版本库添加到APT源：

  ```bash
  sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"
  ```

  #### 3.安装Docker

  ```bash
  sudo apt install docker-ce
  ```



## 拉取LiteLoaderBDS的Docker镜像

- `1.默认为拉取最新版本`

```bash
docker pull shrbox/liteloaderbds
```

- `2.查看已有的docker镜像列表`

```bash
docker image ls 
```

![](docker-bds/docker-image-ls.png)



## 创建&运行docker容器

- `1.创建数据卷,为了能够保存(持久化)数据以及共享容器间的数据,方便日后管理BDS文件`

```bash
docker volume create liteloaderbds
```
![](docker-bds/docker-volume-create.png)
- `2.创建容器`

> Docker 运行相关参数
>
> --name 容器名字
>
> -p 指定容器暴露的端口 主机端口:容器端口/端口协议
>
> -v 映射数据卷 数据卷名字/容器bds文件夹
>
> -t 为容器重新分配一个伪输入终端，通常与 -i 同时使用
>
> -m   指定容器的内存上限 

```bash
docker create --name liteloader -p 19132:19132/udp -m 4G -v liteloaderbds:/home/bds/bds -i -t shrbox/liteloaderbds
```
![](docker-bds/docker-create.png)

- `3.运行容器`

![](docker-bds/docker-ps-a.png)

```bash
docker container start liteloader
```

- `4.对数据卷做一个软连接,直接连接到当前目录下`

```bash
ln -s /var/lib/docker/volumes/liteloaderbds/_data/ liteloader
```

![](docker-bds/ln-s.png)

- `5.最后关于docker一些bds的操作`

> 启动容器&BDS服务器：`docker container start liteloader`
>
> 停止容器&强制停止服务器（不推荐）：`docker container stop liteloader`
>
> 进入容器控制台：`docker attach liteloader` 
>
> `关闭服务器可以进入控制台stop`
>
> 退出控制台：按下 `Ctrl + P + Q`。如果按下 `Ctrl + C`，服务器进程将会终止。



## 插件的使用

在社区下载插件，并将插件直接放入服务器目录下的`plugins`文件夹中即可，然后执行`docker container restart liteloader`重启容器即可，这里我推荐一个提供插件的社区[minebbs](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.minebbs.com%2F)，可自行查找下载。



## liteloader的一些终端命令

- `ll list`
   列出 当前加载的所有插件列表

- `ll load ./plugins/xxxx.js`
   热加载 位于指定路径的插件。路径是相对于 BDS 根目录的相对路径。

- `ll unload xxxx.lua`
   热卸载 在插件列表中名为 xxxx.lua 的插件

- `-ll reload xxxx.dll`
   重新加载 在插件列表中名为 xxxx.dll 的插件

- `ll reload`
   重新加载 插件列表中的所有插件

- `ll version`
   输出当前安装的 LL 版本

- `ll upgrade`
   手动检查 LiteLoaderBDS 自动更新



## 端口开放

`由于我这里是使用的是腾讯云服务器,需要打开防火墙,阿里云应该叫安全组`

```shell
iptables -I INPUT -p udp --dport 19132 -j ACCEPT
```

![](docker-bds/open-port.png)



## 总结

> 使用docker方便在linux或者win快速部署BDS服务器并且使用LiteLoaderBDS加载插件
>
> 此教程根据:[喝甜酒也迷糊](https://www.jianshu.com/p/91922faa8b28)的帖子编写,改编过程可能会出现问题,请大家多多指正
