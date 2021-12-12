title: docker实践1：简单Flask app
url: 279.html
id: 279
categories:
  - 技术研究
  - ''
tags:
  - 技术研究
  - docker
date: 2019-02-25 03:06:00
---
我想部署一个我很久之前写的一个其实不太好的Flask app，然而上次从凌晨3点开始部署到早上8点因为各种环境问题还是没搞定弄得我有点没信心。于是这次决定换一种方法：用docker来部署。

为什么用docker
----------

首先，docker可以有效避免各种由于宿主机的环境导致的问题，比如说我的Ubuntu服务器上python2/3混杂，然后venv不知道怎么回事一直弄不好，然而换用docker之后只需制作好镜像即可在各个地方部署。同时docker的社区已经做起来了，有很多官方的image方便我这种弱手在上面打基础，文档也写得深入浅出。

其次docker可以很方便的把应用的规模放大缩小且做到流量均衡，同时镜像和容器管理也很方便，也有dockerhub这种镜像管理工具。

操作步骤摘要
------

1.安装，参见[https://docs.docker.com/install/overview/](https://docs.docker.com/install/overview/)

2.在应用的同目录下用一定的格式写Dockerfile再以此制作镜像。

3.直接部署。部署时把容器的端口映射到宿主机的某个端口上，这样才能从外部访问容器。

具体操作
----

第一步还是略过，毕竟官方文档教的比我好。

第二步是要准备好你的Python程序。我这里是一个与官方例子较相似的flask web app，首先在此目录下用`pip freeze >requirements.txt`创建依赖文件的记录，然后在程序目录下创建Dockerfile文件并填写以下内容。我这里是从官方文档复制的。
```Dockerfile
    #将官方的python镜像当作base
    FROM python 
    
    # Set the working directory to /app
    WORKDIR /app
    
    # Copy the current directory contents into the container at /app
    COPY . /app
    
    # Install any needed packages specified in requirements.txt
    RUN pip install --trusted-host pypi.python.org -r requirements.txt
    
    # Make port 80 available to the world outside this container端口看情况
    EXPOSE 80
    
    # Define environment variable
    ENV NAME World
    
    # Run app.py when the container launches
    CMD ["python", "app.py"]
```
之后你可以用命令

`docker built -t="你想要的tag" .` （注意最后一个点不要省略，如果你需要push镜像建议tag打成username/repo:tag的格式）来制作镜像。

此时你再用docker images就可以看到自己的制作的镜像了。想运行的话直接使用命令

`docker run -d -p` 映射到宿主机上端口:容器端口 之前的tag

命令用这个格式就可以在后台运行且端口被映射到宿主机上的一个端口。

接下来你就可以为所欲为了。

特殊操作
----

我这里要做的特殊操作有：1.与宿主机SQL进行连接。2.容器内代码可能会更新。

第一个操作的做法比较简单：修改mysql的配置文件使其监听0.0.0.0，同时把程序中用来查数据库的账户的连接权限改成全网。我用的MySQL输入以下命令即可： 

```sql
grant all privileges on *.* to username@'%' identified by 'your password'; FLUSH PRIVILEGES;
```



应用程序内也相应把服务器地址修改为本机的IP而不是127.0.0.1可以了.

第二个操作的话给大家推荐一种简单方法

停止容器 `docker stop 容器id`，可以通过docker container ps 这条命令来查看容器id

`docker cp <container>:<dir of file>  <dir of file> #把容器内文件拷贝到容器外一个位置`

然后修改这个文件

`docker cp <dir of file> <container>:<dir of file> #再把容器外一个文件拷进容器`

最后再启动这个container即可

可以用这个方法来更新容器的程序文件的原理其实是因为每一个docker container有一个文件层，而container里的python实际上是在运行这个文件层的程序。

工作流总结
-----

用docker部署程序一般有这几步：

把东西配置好

写Dockerfile

Build 打tag

上传到云：login然后docker push

Run

跑到别的地方login pull 然后再run