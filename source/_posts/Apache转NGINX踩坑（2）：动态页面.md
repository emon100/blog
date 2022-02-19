title: Apache2转Nginx踩坑（2）：动态页面
url: 247.html
id: 247
categories:
  - 技术研究
date: 2019-02-05 23:21:54
tags:
---
前情提要
----

我已经把环境配置好了，https配置好了，重定向配置好了。现在要要做的是把存放在/var/www/wordpress的wordpress给配置好。如果你不知道我之前做了什么配置，请看前文[Apache转NGINX踩坑（1）：静态页面](/2019/02/05/Apache%E8%BD%ACNGINX%E8%B8%A9%E5%9D%91%EF%BC%882%EF%BC%89%EF%BC%9A%E5%8A%A8%E6%80%81%E9%A1%B5%E9%9D%A2/)

配置
--

wordpress是基于php的博客软件，但Nginx并没有处理php的能力，故我们现在要利用Nginx的接口fastcgi调用解析PHP的php-fpm，这样我们就可以处理php了。

这里很多人直接借鉴网上教程，加上了location ~ \\.php$以及后面的一大串，然而如果你想让你的网站的博客在二级目录下，你就会发现这个写法是有问题的，因为即使你写的多好，浏览器也只会一遍一遍的告诉你file not found. 正确的写法是在特定的location下写上那串代码，这样才能让nginx把正确的文件地址传给fastcgi. 下面是如果我想通过访问emon100.me/wordpress这个子目录来访问wordpress的正确操作：

找到有listen 443 ssl;的那个server区块，向里面**添加**以下内容，仍然用井号代表注释
```Nginx
location /wordpress {
#指定寻找文件用的目录，我用的root，Nginx就会去/var/www/wordpress寻找
#如果我想写的更加清晰易懂，可以写成alias /var/www/wordpress，但是
#下面要额外配置
	root /var/www;
#官方文档推荐的wordpress配置
	location = /wordpress/favicon.ico { 
		log_not_found off; access_log off; 
	}
	location = /wordpress/robots.txt { 
		log_not_found off; access_log off; allow all;
	}
	location ~* \.(css|gif|ico|jpeg|jpg|js|png)$ {
		expires max;
		log_not_found off;
	}
	location ~ /\.ht {
		deny all;
   }    
#关键来了，这里是寻找你的目录下以php结尾的文件，然后用fastcgi给php-fpm处理
#请确保你在上一级已经声明过index index.php这个东西了
	location ~ \.php$ {
		include snippets/fastcgi-php.conf;
	#如果你前面不是root而是用了alias,请加上下面这句以保证可以正确的找到你要的文件
	#fastcgi_param SCRIPT_FILENAME $request_filename;
	#然后这句是传递给php-fpm，我用的php7.2，自行修改版本号
		fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
	}
	try_files $uri $uri/ /wordpress/index.php?$args;
}
```

这样你就可以通过访问/wordpress子目录来访问wordpress了，当然你如果想把子目录改成/blog也可以，那么要把其中的root按照例子改成alias且按照指示改一下php区块下的内容。

以上是Nginx处理动态页面的一个例子。希望还是不要太依赖教程，多看看文档。