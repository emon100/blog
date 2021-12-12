---
title: 如何通过设置使得wordpress需通过访问域名下子目录才可访问
url: 30.html
id: 30
categories:
  - 技术研究
date: 2018-10-24 00:22:58
tags:
---
我的条件：服务器系统ubuntu 18.04 apache2 且之前在000-defaulf.conf中设置根目录为/var/www/wordpress/ 

设置一共3步： 

1.去wordpress设置中将博客地址改为http://域名/wordpress 

2.此时域名已无法访问，此时去找到你服务器上apache2的sites-available中的000-default.conf 将document-root改为wordpress的上级目录，此处为/var/www/ 

3.shell里输入 sudo systemctl restart apache2 目的是重启apache2 打开 http://域名/worepress ，发现此时已可以访问。 如果操作失败，则各项刚刚修改的项目均需还原修改前的样子并看我前文教程来恢复对wordpress的访问。