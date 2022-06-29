---
title: Nginx+Tomcat实现负载均衡

date: 2022-5-25 21:17:46

---

# Nginx + Tomcat 实现负载均衡

## 1. 环境

~~~~~
Linux version:  5.4.0-96-generic
OS Version:     ubuntu1~20.04
Architecture:   amd64
Nginx version:  nginx/1.18.0 (Ubuntu)
JVM Version:    11.0.15+10-Ubuntu-0ubuntu0.20.04.1
Tomcat Version: Apache Tomcat/9.0.63
~~~~~



## 2. 安装

### 2.1 安装 Nginx

在命令行输入：

~~~
sudo apt-get install nginx
~~~

测试命令：

~~~
sudo nginx -t
~~~

窗口显示：

~~~
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
~~~

在浏览器输入服务器 ip：

![image-20220525212228322](https://s2.loli.net/2022/05/25/YhJTEsLDv8x1BkN.png)



### 2.2 安装 Java

安装 jdk：

~~~
sudo apt-get -y install openjdk-11-jdk
~~~

查看版本：

~~~
java --version
~~~

输出如下：

~~~
openjdk 11.0.15 2022-04-19
OpenJDK Runtime Environment (build 11.0.15+10-Ubuntu-0ubuntu0.20.04.1)
OpenJDK 64-Bit Server VM (build 11.0.15+10-Ubuntu-0ubuntu0.20.04.1, mixed mode, sharing)
~~~

安装成功。

查看 Java 的安装位置：

~~~
update-alternatives --config java
~~~

输出如下：

~~~
There is only one alternative in link group java (providing /usr/bin/java): /usr/lib/jvm/java-11-openjdk-amd64/bin/java
~~~

将 $JAVA_HOME 添加到环境变量：

~~~
sudo vim $HOME/.bashrc
shift+G
~~~

在行尾添加：

~~~
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
export PATH=$PATH:$JAVA_HOME
~~~

退出 `vim` ，激活环境变量：

~~~
source $HOME/.bashrc
~~~

验证环境变量：

~~~
echo $JAVA_HOME
~~~

输出如下，激活成功。

~~~
/usr/lib/jvm/java-11-openjdk-amd64
~~~



### 2.3 安装 Tomcat

官网下载的 `Tomcat 9.0.63`，注意需为 `Core/tar.gz(pgp, sha512)` 版本，或 <a href="https://bucket011.obs.cn-north-4.myhuaweicloud.com:443/apache-tomcat-9.0.63.tar.gz?AccessKeyId=S3NBXULCUVSGOWLC75R3&Expires=1684599167&Signature=XI0c%2BNskc/yf%2BTBPgBEbnSt%2BNs0%3D">点此下载</a> ，将文件上传到服务器中的路径 `/usr/local` 下，输入下列命令解压：

~~~
cd /usr/local && tar -zxvf apache-tomcat-9.0.63.tar.gz
~~~

配置环境变量，打开 `Tomcat` 启动脚本：

~~~
cd apache-tomcat-9.0.63 && vim bin/startup.sh
shift+G
~~~

在语句 `exec "$PRGDIR"/"$EXECUTABLE" start "$@"` 的上一行插入下列语句：

~~~
JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
JRE_HOME=$JAVA_HOME/jre
PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME
CLASSPATH=.:$JRE_HOME/lib/rt.jar:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
TOMCAT_HOME=/usr/local/apache-tomcat-9.0.63
~~~

保存并退出。

开放服务器的 `8080` 端口：

~~~
firewall-cmd --zone=public --add-port=8080/tcp --permanent
~~~

重启防火墙

~~~
systemctl restart firewalld.service
~~~

去云服务器管理控制台添加安全组：

![image-20220525234438235](https://s2.loli.net/2022/05/26/oLmy6BXbPAI4wf5.png)

运行：

~~~
./bin/shutdown.sh && rm -rf logs/catalina.out && ./bin/startup.sh ; tail -f logs/catalina.out
~~~

输出应该如下所示：

![image-20220526002554379](https://s2.loli.net/2022/05/26/JtmZ4eRK3hX8sf1.png)

在浏览器访问 `$ip:8080`，显示：

![image-20220526002245669](https://s2.loli.net/2022/05/26/iEMRucdl8Ws7oGq.png)

说明你配置成功。



## 3. 部署

### 3.1 Tomcat 服务器部署

本实验需要使用 3 个端口，分别使用 `8001`，`8082`，`8083`，在腾讯云控制台开放端口后，在 `shell` 中手动开放端口并重启防火墙：

~~~
firewall-cmd --zone=public --add-port=8081/tcp --permanent
firewall-cmd --zone=public --add-port=8082/tcp --permanent
firewall-cmd --zone=public --add-port=8083/tcp --permanent
systemctl restart firewalld.service
~~~

我们使用 `Tomcat` 自带的 `3` 个 `Web` 项目来实现不同的端口访问不同的应用，假设你完全按照上文的方式操作，那么此时你的 `Tomcat` 为 `/usr/local/apache-tomcat-9.0.63`，根据根路径打开服务器的配置文件：

~~~
cd /usr/local/apache-tomcat-9.0.63/conf && vim server.xml
~~~

删除掉原代码，换成以下代码：

~~~
<?xml version="1.0" encoding="UTF-8"?>
<Server port="8005" shutdown="SHUTDOWN">
     <Listener className="org.apache.catalina.startup.VersionLoggerListener" />
     <Listener className="org.apache.catalina.core.AprLifecycleListener" SSLEngine="on" />
     <Listener className="org.apache.catalina.core.JreMemoryLeakPreventionListener" />
     <Listener className="org.apache.catalina.mbeans.GlobalResourcesLifecycleListener" />
     <Listener className="org.apache.catalina.core.ThreadLocalLeakPreventionListener" />
     <GlobalNamingResources>
     <Resource name="UserDatabase" auth="Container"
               type="org.apache.catalina.UserDatabase"
               description="User database that can be updated and saved"
               factory="org.apache.catalina.users.MemoryUserDatabaseFactory"
               pathname="conf/tomcat-users.xml" />
     </GlobalNamingResources>

     <Service name="Catalina">
          <Connector port="8081" protocol="HTTP/1.1"
                    connectionTimeout="20000"
                    redirectPort="8443" />
               <Engine name="Catalina" defaultHost="localhost">
                    <Realm className="org.apache.catalina.realm.LockOutRealm">
                    <Realm className="org.apache.catalina.realm.UserDatabaseRealm"
                         resourceName="UserDatabase"/>
                    </Realm>
                    <Host name="localhost"  appBase="webapps"
                         unpackWARs="true" autoDeploy="true">
                         <Context path="" docBase="ROOT" reloadable="true" />
                         <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
                              prefix="localhost_access_log" suffix=".txt"
                              pattern="%h %l %u %t &quot;%r&quot; %s %b" />
                    </Host>
               </Engine>
     </Service>

     <Service name="Catalina2">
          <Connector port="8082" protocol="HTTP/1.1"
                    connectionTimeout="20000"
                    redirectPort="8443" />
               <Engine name="Catalina2" defaultHost="localhost">
                    <Realm className="org.apache.catalina.realm.LockOutRealm">
                    <Realm className="org.apache.catalina.realm.UserDatabaseRealm"
                         resourceName="UserDatabase"/>
                    </Realm>
                    <Host name="localhost"  appBase="webapps"
                         unpackWARs="true" autoDeploy="true">
                         <Context path="" docBase="examples" reloadable="true" />
                         <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
                              prefix="localhost_access_log" suffix=".txt"
                              pattern="%h %l %u %t &quot;%r&quot; %s %b" />
                    </Host>
               </Engine>
     </Service>

     <Service name="Catalina3">
          <Connector port="8083" protocol="HTTP/1.1"
                    connectionTimeout="20000"
                    redirectPort="8443" />
               <Engine name="Catalina3" defaultHost="localhost">
                    <Realm className="org.apache.catalina.realm.LockOutRealm">
                    <Realm className="org.apache.catalina.realm.UserDatabaseRealm"
                         resourceName="UserDatabase"/>
                    </Realm>
                    <Host name="localhost"  appBase="webapps"
                         unpackWARs="true" autoDeploy="true">
                         <Context path="" docBase="examples/websocket" reloadable="true" />
                         <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
                              prefix="localhost_access_log" suffix=".txt"
                              pattern="%h %l %u %t &quot;%r&quot; %s %b" />
                    </Host>
               </Engine>
     </Service>
</Server>

~~~

这样，通过 `$ip:8081`、`$ip:8082`、`$ip:8083` 分别访问就可以得到不同的服务端。

> 8083 端口的资源不完整，因为 Tomcat 默认只提供了 2 个有效的 app，第三个端口会返回一个 404 页面，某种程度上它也是一个应用。

### 3.2 Nginx 反向代理

编辑 `nginx.conf` 配置文件：

~~~
vim /etc/nginx/nginx.conf
~~~

将下列语句插入到模块 `http` 的末尾：

~~~
upstream tomcat_server {
    server 101.42.117.143:8081;
    server 101.42.117.143:8082;
    server 101.42.117.143:8083;
}

server {
    listen 80;
    server_name 101.42.117.143;

    location / {
        proxy_pass http://tomcat_server;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
~~~

启动或重启 `nginx`：

~~~
nginx 或 nginx -s reload
~~~

这样，可以直接访问 `80` 端口，或 `$ip` 后将其代理到后端的 3 台服务器上。

> nginx 默认采用轮询策略。



## 4. 负载均衡

### 4.1 轮询

轮询策略 `nginx.conf` 的配置如下：

~~~
upstream tomcat_server {
    server 101.42.117.143:8081 weight=1;
    server 101.42.117.143:8082 weight=1;
    server 101.42.117.143:8083 weight=1;
}

server {
    listen 80;
    server_name 101.42.117.143;

    location / {
        proxy_pass http://tomcat_server;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
~~~

重启 `nginx`：

~~~
nginx -s reload
~~~

使用 `postman` 频繁访问，页面出现的频率是 `ABC ABC ABC ABC`。



### 4.2 加权轮询

加权轮询策略 `nginx.conf` 的配置如下：

~~~
upstream tomcat_server {
    server 101.42.117.143:8081 weight=1;
    server 101.42.117.143:8082 weight=3;
    server 101.42.117.143:8083 weight=5;
}

server {
    listen 80;
    server_name 101.42.117.143;

    location / {
        proxy_pass http://tomcat_server;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
~~~

重启 `nginx`：

~~~
nginx -s reload
~~~

使用 `postman` 频繁访问，页面出现的频率是 `CBCACBCBC CBCACBCBC`。

该算法称为提案者轮换选择算法，每一轮都会将被选中的对象减去总权重值，每一轮都会将各结点的权重值加上固定的预设权重值，这是一个公平的轮换算法，可以防止权重过大的结点持续占据资源。过程如下：

| 初始权重 |  加权  |  选择  | 选中者减去权重和 |
| :------: | :----: | :----: | :--------------: |
|  0 0 0   | 1 3 5  |   C    |      1 3 -4      |
|  1 3 -4  | 2 6 1  |   B    |      2 -3 1      |
|  2 -3 1  | 3 0 6  |   C    |      3 0 -3      |
|  3 0 -3  | 4 3 2  |   A    |      -5 3 2      |
|  -5 3 2  | -4 6 7 |   C    |     -4 6 -2      |
| -4 6 -2  | -3 9 3 |   B    |      -3 0 3      |
|  -3 0 3  | -2 3 8 |   C    |     -2 3 -1      |
| -2 3 -1  | -1 6 4 |   B    |     -1 -3 4      |
| -1 -3 4  | 0 0 9  |   C    |      0 0 0       |
|  0 0 0   | 1 3 5  |   C    |      1 3 -4      |
|  1 3 -4  | 2 6 1  |   B    |      2 -3 1      |
|  2 -3 1  | 3 0 6  |   C    |      3 0 -3      |
|  ......  | ...... | ...... |      ......      |



### 4.2 IP Hash

轮询策略 `nginx.conf` 的配置如下：

~~~
upstream tomcat_server {
    server 101.42.117.143:8081;
    server 101.42.117.143:8082;
    server 101.42.117.143:8083;
    ip_hash;
}

server {
    listen 80;
    server_name 101.42.117.143;

    location / {
        proxy_pass http://tomcat_server;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
~~~

重启 `nginx`：

~~~
nginx -s reload
~~~

它会将主机的 `$ip` 哈希映射到一个随机的固定值，然后对 `3` 取模得到响应的端口序号；

使用主机 1 的 `postman` 频繁访问，页面出现的频率是 `AAAAAA`；

使用主机 2 的 `postman` 频繁访问，页面出现的频率是 `BBBBBB`。
