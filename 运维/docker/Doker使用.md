# Doker使用

#### Docker的下载与安装:

推荐使用Ubuntu 或 CentOS7及以上.

ubuntu是以桌面应用为主的Linux操作系统.

安装命令: yum install docker

 

#### 卸载docker: 

1. 查看当前docker安装了什么 --- yum list installed | grep docker 

2. 删除刚才显示的包 

3. 删除docker的镜像 --- rm -rf /var/lib/docker/

4.校验是否删除干净 --- yum list installed | grep docker 

 

 

#### docker的操作:

1. systemctl start docker --启动docker

2. systemctl status docker --查看docker的启动状态

3. systemctl stop docker --关闭docker

4.systemctl restart docker --重启docker

5.doker info -- 查看docker的概要信息

6.docker -help 查看docker的帮助文档

 

#### Docker镜像操作:

\1. docker images -- 列出镜像

\2. docker seach tomcat -- 查找镜像

\3. docker pull 镜像名称 -- 拉取镜像

\4. docker rmi 镜像名称/镜像id

5.docker rmi `docker images -q`; 删除所有镜像

 

 

#### Docker容器操作:

\1. docker ps -- 查看容器信息

2.docker ps -a -- 查看所有容器(包括正在运行及之前运行的容器)

\3. docker ps -l -- 查看最后一次运行的容器

\4. docker ps -f status=exited -- 查看停止的容器

 

#### 创建容器的命令 :

docker run 创建容器

-i --运行容器

-t --容器创建成功以后我们可以进入到容器中

-d --表示让容器在后台运行

--name --用来指定我们创建容器的名称

-v --用来指定目录映射 --> 指定宿主机的某一个目录和容器中某一个目录的对应关系

-p --用来指定端口映射 --> 指定宿主机的某一个端口和同期中运行的软件端口的对应关系

 

创建一个交互式容器: docker run -it --name=容器自定义名字 镜像名 /bin/bash (liunx常用解析器)

创建一个守护式容器 : docker run -di --name=自定义名字 镜像名 /bin/bash

进入守护式容器 : docker exec -it 容器自定义名/容器id /bin/bash

停止守护式容器 : docker stop 容器名 ps: 关闭容器会比较慢

启动之前的创建好的容器: docker start 容器名