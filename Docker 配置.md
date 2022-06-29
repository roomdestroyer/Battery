---
title: Docker 配置过程

date: 2022-04-11 12:39:19

tags:
---

#### Docker 配置过程

---

1. 更新软件包缓存

   ~~~
   sudo apt-get update
   ~~~

2. 安装需要的包

   ~~~
   sudo apt-get install apt-transport-https ca-certificates software-properties-common curl
   ~~~

3. 添加 GPG 秘钥，并添加中科大 Docker-ce 软件源

   ~~~
   curl -fsSL https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
   ~~~

   ~~~
   sudo add-apt-repository "deb [arch=amd64] https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
   ~~~

   更新软件包缓存

   ~~~
   sudo apt-get update
   ~~~

4. 安装 Docker-ce

   ~~~
   sudo apt-get install docker-ce
   ~~~

5. 测试运行

   ~~~
   sudo docker run hello-world
   ~~~

   输出以下内容表示配置成功：

   ![image-20220411125032631](https://s2.loli.net/2022/04/11/xPblFJyZBca1mDr.png)



#### Docker 基本使用

---

1. 查看 docker 镜像

   ~~~
   docker images
   ~~~

​		 ![image-20220411125312074](https://s2.loli.net/2022/04/11/PDpq2FXiBLSZyaG.png)

2. 查看docker容器

   ~~~
   docker ps
   ~~~

   ![image-20220411125334731](https://s2.loli.net/2022/04/11/aUlV3xQuHTLqyjB.png)

- 镜像与容器的关系：镜像是一个 linux 虚拟机，容器是启动虚拟机，每次启动的时候，是一个副本，是先把镜像复制一份后直接启动，启动不干扰镜像，一个镜像可以启动无数个容器。

3. 启动 docker

   ~~~
   docker start 容器名称
   # 或者 docker start 容器id
   ~~~

4. 停止 docker

   ~~~
   docker stop 容器名称
   ~~~

5. 进入docker容器

   ~~~
   docker attach 容器名称
   ~~~

   