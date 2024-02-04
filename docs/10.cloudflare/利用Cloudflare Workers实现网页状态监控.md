---
title: 利用Cloudflare Workers实现网页状态监控
date: 2024-02-04 19:09:23
permalink: /pages/2971cd/
categories:
  - cloudflare
tags:
  - cloudflare
  - 'cloudflare workers'
author: 
  name: violets007
  link: https://github.com/violets007
titleTag: 原创 # v1.9.0+
---

![](https://img.violets007.cn/file/0cf845ba3e6deb4211376.png)
::: tip
直接监控你的网站状态
:::

<!-- more -->




## 1.首先Fork 
[cf-workers-status-page](https://github.com/eidam/cf-workers-status-page)

## 2.浏览器地址栏输入

https://deploy.workers.cloudflare.com/?url=https://github.com/`$YourUserName`/cf-workers-status-page

## 3.获取Cloudflare账户内的Account ID和API Token

![](https://img.violets007.cn/file/1b0a9db3d051a53d3777a.png)

::: tip
**授权的token需要workes的编辑权限**
:::

![](https://img.violets007.cn/file/5feb9b1e3406fcee170d6.png)

## 4.在actions选择Deploy点击开启
::: warning
一定要在Deploy开启,不然无法部署成功
:::

![](https://img.violets007.cn/file/898bff37ea3106e886d99.png)

## 5.最后修改定时更新CRON

![](https://img.violets007.cn/file/6d318f5a4c7b0bd0cb3e2.png)

crons = ["*/2 * * * *"]



## 6.最后还可以设置你的二级域名,配置自己的网站直接查看项目的readme

::: warning
项目地址
:::

[https://github.com/eidam/cf-workers-status-page](https://github.com/eidam/cf-workers-status-page)