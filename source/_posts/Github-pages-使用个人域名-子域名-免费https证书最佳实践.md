title: Github pages 使用个人域名+子域名+免费https证书最佳实践
author: Emon Wang
tags:
  - Web
categories:
  - 技术研究
date: 2020-01-28 18:32:00
---
# 前言
github早在2018年5月1日就已经与[Let's Encrypt](https://letsencrypt.org/)合作，宣布支持对用户提供的个人域名颁发https证书。然而目前在国内搜索Github pages的教程时大部分的作者还停留在使用CDN颁发的证书的阶段。现在我来和大家分享我摸索出的一套使用个人域名+子域名+使用免费https证书的最佳实践。

本文假定读者已经成功部署了自己的网站且使用了子域名，但未使用Github提供的https证书以及未开启Github提供的https服务。


# 正文

步骤：
1. 特殊的修改域名解析以使得Github有条件给你一个https证书。
2. 重启各个仓库的强制https选项让Github给你https访问功能。

关键点是，Github需要你的域名解析CAA记录才能给你颁发证书，像我一样添加一项CAA记录：

|域名|类型 |值                |
|-  | -   |    -              |
|   |A	  | 185.199.111.153   |
|   |CAA  |0:issuewild:letsencrypt.org	|	
|blog|	CNAME|	emon100.github.io	|	
|www |	CNAME|	emon100.github.io|

等待一段时间，域名解析记录刷新，这时可以去你的所有Github pages的仓库设置里把设置刷新：
![Github pages的设置](../../../../img/paste/pasted-5.png)
1. 先把下面的custom domain删了保存。
2. enforce https关了，等待Github应用设置（之前打不开的话不管此条）。
3. 再重新把原来的custom domain添加回去，enforce https打开，此时应该会看到Github提示你晚点设置会生效而不是之前的无法打开enforce https。
4. 等待。

等待之后之后你就会惊喜的发现你的域名可以用https访问了。


# 优化访问速度最佳实践

使用CDN，如我使用了Cloudflare，注册个帐号之后，跟着指示做即可，很省心。使用之后打开网站和加载图片的速度变得非常快。