---
title: 将我的网站从Http转到Https
url: 61.html
id: 61
categories:
  - 技术研究
date: 2018-11-24 10:51:26
tags:
  - web
---
昨天是黑色星期五，我原准备买一个便宜点域名和一个新的VPS，然而Namecheap和Bandwagon的促销活动都萎的不行，个人猜测可能是因为受到了贸易战的影响。于是只好使用GitHub的学生包领取了一个长达一年的.me域名和一个长达一年的SSL证书。下面我来写写步骤。

服务器环境：LAMP Ubuntu18.04 Wordpress

首先是参照的教程[https://www.digitalocean.com/community/tutorials/how-to-install-an-ssl-certificate-from-a-commercial-certificate-authority](https://www.digitalocean.com/community/tutorials/how-to-install-an-ssl-certificate-from-a-commercial-certificate-authority)

其实很简单，重点步骤是1.申请证书

2.修改Apache2的配置   

3.修改wordpresss的配置

申请证书不多写了，这个过程可以单独写一篇文章，去百度搜索Namecheap申请证书或者看上面的文章就可以了。注意生成CSR的时候要把common name填写成自己的域名，然后在申请的时候DCV Methods in Use建议使用DNS，这样只要根据要求设置指定CNAME和映射几分钟就能把证书给你。之前我用的大家推荐的Email方法，非常慢。

![](http://blog.emon100.me/img/未命名图片.png)

password已经打码了，这是生成csr的时候

之后就是把证书传进服务器。要传入一个好找的位置，我是跟csr和key放在一起的。之后再设置Apache2的设置，和301重定向，不多写了，上面的教程也有。

最后是设置wordpress，我用的phpadmin来设置，将wordpress数据库中wp-options中home和siteurl都设置为带https的url即可正常访问wordpress。