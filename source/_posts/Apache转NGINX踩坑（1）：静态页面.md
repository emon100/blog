title: Apache2转Nginx踩坑（1）：静态页面
url: 236.html
id: 236
categories:
  - 技术研究
tags:
  - Web
date: 2019-02-04 00:45:59
---
为什么要转
-----

先说一下情况，环境是Ubuntu 18.04+LAMP，除了首页的静态页面之外我还有Wordpress和一个flask webapp. Apache2**并不是做的不好**，只是我遇到了2个**我真的不想解决的问题**导致我选择抛弃他：

1.我理解不能的文档。Apache2的文档是挺漂亮的。然而我一开始想把我的网站全面转成https时在官方文档里我根本找不到例子，（有例子，然而关键词根本不是example 而是我没意识到的How-to），然后要看懂后面的文档必须把前面的全部看懂，(因为不是例子)。最后我只好靠百度+CSDN在我的配置文件里添删改了无数内容在bash开启了不知道多少个apache2的模组才最终把https打开，最后可以说是莫名其妙It works. 相比之下Nginx的文档清晰的用例子告诉你跑过去在服务器配置文件里加两句ssl模组打开再加两句把证书位置指明然后就开了https了，真方便。

说到这又要说了，apache2.4是现在的最新版本，然而国内的资料，包括中文文档还停留在apache2.2，这说明如果我想上手apache2.4就必须去读官方英文文档，我当时没时间，现在有时间了，自然选择从零开始学习Nginx了。

2.我一直没看懂的配置文件语法。我真的难以理解apache2里面的rewrite规则之类的怎么写，这个其实不怪官方，怪我自己没仔细看文档，但也是因为这个原因，把wordpress部署到子目录这个很简单的工作花了我很长时间，把http流量301重定向到https也花了很长时间瞎改，这个被我瞎改改的臃肿的配置文件我已经无法忍受了。相比之下Nginx的配置文件就有条理多了。我看了一会文档里的例子和说明，然后举一反三很快就写出来了。

预备工作
----

在控制台输入以下内容：
```shell
sudo /etc/init.d/apache2 stop
```
以将apache2的服务停止，否则因为Nginx和apache2同时使用php可能会导致后面出现安装失败。
```shell
sudo apt install Nginx 
```
> php-fpm(FastCGI Process Manager) 是一个用于在服务器和php后端数据交互的接口，让服务器（Nginx）直到，对于 *.php 文件，首先要给php解释器执行，然后把结果传回服务器，再返回给请求者。CGI实现了这个接口，FastCGI以更快地方式实现，而php-fpm也就是一个fastCGI的php官方版本。
> 
> 我觉得之前LAMP一波装上apache2的应该不用另外装php-fpm，但如果不是的话请  
> sudo apt install php-fpm

配置
--

ok，这下搞定了所有预备工作，接下来就是开始配置Nginx的工作了。首先是我的简陋的主页，这是一个静态网页index.html，存放在/var/www/html，用这个我们可以先测试https的配置是否正确，由于nginx默认是开着ssl模块的，所以我们可以写的很轻松。

进入/etc/nginx/sites-available查看配置文件default，里面给出了例子，这里我们来仿写和修改吧
```Nginx
#井号开头是注释，这里用这个方法把下面仿写的解释清楚
#server是Nginx里面的类似虚拟主机的概念
server {
#原本有listen 80和listen[::]:80 我因为要使用https故
#使用了下方监听了443端口的配置，这里用参数ssl声明所用的协
#议，用default_server来说明这是默认的主机，
#Nginx里每句话结束用分号
    listen 443 ssl default_server; 
    listen [::]:443 ssl default_server;
#ssl证书的存放位置，我这里填的我的
    ssl_certificate     /home/emon100/emon100.me.crt;
    ssl_certificate_key /home/emon100/emon100.me.key;
#服务器的监听的url为这些，我填的我的
    server_name emon100.me www.emon100.me;
#声明主页文件类型为下面这些，由于我还要
#配置wordpress故先加上index.php
    index index.php index.html index.htm;
#现在在书写访问根目录时的情况
    location / {
	#声明根目录是哪个，用这个我们也可以把域名二级
	#目录对应到服务器其他位置
		root /var/www/html;
	#Nginx有许多自带的变量，变量名前有$，此处为
	#变量uri，如emon100.me/1/2/index
	#那么uri就是/1/2/index，这里先把请求看做
	#文件，再看做目录，再返回404
		try_files $uri $uri/ =404;
    }
}
```

写的过程中我们会发现作者提醒ssl如果开gzip那么会出bug，这里我们再打开  
/etc/nginx/nginx.conf找到gzip改成off.

接下来我们可在bush输入nginx -s stop关闭nginx 再输入nginx 打开nginx，可以看到熟悉的主页了，且可以用https访问了。更进一步，未来所有的子目录都可能会用上https，所以我们要做两件事：

1.把证书应用到所有虚拟主机下。

2.把所有http访问301重定向到https去。

第一件事很好解决，只需要把ssl的证书位置那两行移到server的大括号外面即可，这样就是全局应用了

第二件事我们只需再开一个虚拟主机，无需使用rewrite，只需使用return（重定向功能）即可，在配置文件下再加上以下几行：
```Nginx
server {
    listen          80;
    listen          [::]:80;
    server_name emon100.me www.emon100.me;
    #第一个参数表示301重定向，后面是重定向
    #之后的url，用两个变量组合应对各种可能
    return 301 https://$server_name$request_uri;
}
```
再用上面提到的两个命令重启nginx，用http连接一下主页，发现已经重定向到了https的网址。那么我们的任务1：应对静态网页就完美解决了！