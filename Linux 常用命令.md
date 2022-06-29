## Linux常用命令

1. 查看内存容量及使用情况： 

   ~~~shell
   free -lh
   ~~~

2. 以GB为单位显示磁盘使用量和占用率：

   ~~~shell
   df -lh ./
   ~~~

3. 查看CPU信息(包括型号、主频、内核信息等)：

   ~~~shell
   cat /proc/cpuinfo
   ~~~

4. 查看当前操作系统版本信息：

   ~~~shell
   cat /proc/version
   ~~~

5. 查看当前版本操作系统内核信息：

   ~~~
   uname -a
   ~~~

6. 查看ip信息：

   ~~~shell
   ip a
   ~~~

7. 重启网络：

   ~~~
   CentOS: service network restart
   Ubuntu: sudo systemctl restart NetworkManager
   ~~~

8. 查看防火墙状态（不要用 `ufw`）：

   ~~~
   systemctl status firewalld.service
   ~~~

9. 关闭防火墙：

   ~~~
   systemctl stop firewalld.service
   ~~~

10. 打开防火墙：

    ~~~
    systemctl start firewalld.service
    ~~~

11. 重启防火墙

    ~~~
    systemctl restart firewalld.service
    ~~~

12. 开放端口

    ~~~
    firewall-cmd --zone=public --add-port=3000/tcp --permanent
    ~~~

13. 查看已开放的端口：

    ~~~
    firewall-cmd --zone=public --list-ports
    ~~~

14. 查看监听的端口：

    ~~~
    netstat -lnpt
    ~~~

15. 服务器ping不通github：

    - 打开网址：

      ~~~
      https://www.ipaddress.com/
      ~~~

    - 搜索框搜索：

      ~~~
      github.com
      ~~~

    - 找到域名对应的 ip ，假设是 140.82.112.3：

       ~~~
       vim /etc/hosts
       ~~~

    - 加入以下字段：

      ~~~
      140.82.112.3 github.com
      ~~~

16. 添加软链接（环境变量）：

    ~~~
    sudo ln -s /*** /usr/bin/***
    ~~~

17. 没有 ll 命令：

    - 编辑配置文件：

      ~~~
      vim ~/.bashrc
      ~~~

    - 设置别名，添加如下行：

      ~~~
      alias ll='ls -alF'
      ~~~

    - 重启配置文件：

      ~~~
      source ~/.bashrc
      ~~~

18. kill名为 *** 的的进程

    ~~~
    ps aux|grep django|grep -v grep|cut -c 9-16|xargs kill -9
    ~~~

19. 查看进程树：

    ~~~
    pstree -ap|grep django
    ~~~

20. kill 占用特定 ip 端口的进程：

    ~~~
    netstat -anp|grep 9100|cut -c 81-85|xargs kill -9
    ~~~

21. 查看进程占用的 ip 端口：

    ~~~
    netstat -anp|grep LISTEN|grep polkadot
    ~~~

22. 添加用户：

    ~~~
    useradd -m newuser
    passwd newuser
    ~~~

23. 切换到用户模式：

    ~~~
    sudo -u newuser -i /bin/bash
    ~~~

24. 切换到 root 模式：

    ~~~
    su
    ~~~

25. docker：

    ~~~
    download: curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
    restart:  sudo service docker restart
    ~~~

26. 编辑 Golang 环境变量：

    ~~~
    sudo vim $HOME/.bashrc
    shift+G
    export go=/usr/local/go/bin
    export GOROOT=/usr/local/go
    export GOPATH=/home/go
    export PATH=$PATH:$GOROOT/bin:$GOPATH/bin:$go
    source $HOME/.bashrc
    go env
    ~~~

27. 解压文件：

    ~~~
    tar -zxvf *** -C /***
    ~~~

28. DNS 查询：

    ~~~
    dig zhoujinsdu
    ~~~

29. 查看当前是否使用代理：

    ~~~
    git config --global http.proxy 
    ~~~

    取消代理：

    ~~~
    git config --global --unset http.proxy
    git config --global --unset https.proxy
    ~~~

30. 查看实时网速：

    ~~~
    iptraf -g
    ~~~

31. get substrate dependencies:

    ~~~
    curl https://getsubstrate.io -sSf | bash
    substrate --dev
    ~~~

32. get rust and required tools:

    ~~~
    curl https://sh.rustup.rs -sSf | sh
    make init
    ~~~

33. 生成requirements.txt:

    ~~~
    pip3 freeze > requirements.txt
    ~~~

34. 安装requirements.txt:

    ~~~
    pip3 install -r requirements.txt
    ~~~

35. git 提交项目：

    ~~~
    git init
    git add .
    git commit -m "feat: Completed"
    git remote rm origin
    git remote add origin git@github.com:roomdestroyer/MicroCPUMachine.git
    git remote -v
    git checkout -b main
    git pull origin main --allow-unrelated-histories
    git push -f -u origin main
    roomdestroyer
    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
    ~~~

36. git 更新项目：

    ~~~
    git pull
    sudo git add .
    sudo git commit -m "feat: Updated"
    sudo git push
    elford2333asd
    ~~~

37. git 编辑 origin：

    ~~~
    git remote -v
    git remote rm origin
    ~~~

38. 实时打印日志：

    ~~~
    tail -f -n 100 geth.log
    ~~~

39. 使用 root 用户登录实例：

    ~~~
    sudo passwd root
    === 输入密码
    sudo vi /etc/ssh/sshd_config 
    === 找到 #Authentication，将 PermitRootLogin 参数修改为 yes
    === 找到 #Authentication，将 PasswordAuthentication 参数修改为 yes
    === 保存并退出
    sudo service ssh restart
    ~~~

40. 新增用户并分配权限：

    ~~~
    sudo useradd -r -m -s /bin/bash user
    # 删除用户
    sudo userdel user
    sudo rm -rf /home/user
    ~~~

41. mysql :

    ~~~
    # 查看 mysql 依赖项
    dpkg --list|grep mysql
    # 卸载 mysql
    sudo apt-get remove mysql-common
    sudo apt-get autoremove --purge mysql-server-5.7
    dpkg -l|grep ^rc|awk '{print$2}'|sudo xargs dpkg -P
    sudo apt-get autoremove --purge mysql-apt-config
    rm -rf /etc/mysql && -rm -rf /var/lib/mysql
    # 安装 mysql
    sudo apt install mysql-server mysql-client
    sudo cat /etc/mysql/debian.cnf
    mysql -udebian-sys-maint -p***
    # 修改 root 账号密码
    mysql> use mysql;select host, user, authentication_string, plugin from user;         
    mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '1234';
    mysql> update user set  plugin="mysql_native_password";     
    mysql> flush privileges;
    # 使用 root 登录，设置远程连接
    mysql> use mysql;select host, user, authentication_string, plugin from user;   
    mysql> CREATE USER 'root'@'%' IDENTIFIED BY '1234';
    mysql> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%';
    mysql> ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '1234';
    mysql> flush privileges;
    mysql> quit;
    sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
    # bind-address          = 127.0.0.1
    sudo service mysql restart
    # 启动 mysql
    sudo service mysql start
    # 停止 mysql
    sudo service mysql stop
    # 重启 mysql
    sudo service mysql restart
    # 跳过验证
    vim /etc/mysql/mysql.conf.d/mysqld.cnf
    在[mysqld]下面追加：skip-grant-tables
    ~~~

42. `pip` 更新

    ~~~
    python -m pip install --upgrade pip
    ~~~

    

