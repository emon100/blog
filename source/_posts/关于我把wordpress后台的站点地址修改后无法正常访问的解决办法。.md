title: 关于我把wordpress后台的站点地址修改后无法正常访问的解决办法。
url: 11.html
id: 11
categories:
  - 技术研究
tags:
  - MySQL
date: 2018-10-09 10:12:00
---
我使用的方法是修改MySQL里面中的数据

首先登陆到自己的服务器后输入：

```shell
    mysql -u root -p
```

再输入密码，以使用root账户登录服务器，我之前设置过root为mysql管理账户，你应当输入你设置的账户。  

切换到你的wordpress的数据库，以我为例，我的wordpress数据库名字为wordpress  
输入（注意分号）：

```SQL
USE wordpress;
    
UPDATE wp_options SET option_value = replace(option_value, '你输错的URL','正确的URL') WHERE option_name = 'home' OR option_name = 'siteurl';
```
然后再输入以下指令，把“输错的URL”和“正确的URL”按你自己的情况修改。

还有一种方法2，即进入你的phpmyadmin页面，如http://example.com/phpmyadmin/ （example.com是你的地址）选择你的wordpress数据库，再选择wp_options一项，修改其中的home和siteurl两项为正确的地址，这个方法更简单，但是我之前没来得及用，之后实测此方法是有效的。