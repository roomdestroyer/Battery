# Github 部署 Hexo 个人博客

以下命令按顺序来，前后路径需要对应，命令千万不要改变，部署成功了再去研究具体原理：

# 1. 安装依赖

打开网址： `https://registry.npmmirror.com/binary.html?path=node/v17.6.0/`
选择 `node-v17.6.0-linux-x64.tar.gz` 下载，放到目录 `/usr/local` ，依次使用以下命令：

```bash
tar -xvzf node-v17.6.0-linux-x64.tar.gz
sudo ln -s /usr/local/node-v17.6.0-linux-x64/bin/node /usr/bin/node
sudo ln -s /usr/local/node-v17.6.0-linux-x64/bin/npm /usr/bin/npm
node -v
npm -v
apt install git
git --version
npm install -g hexo-cli
rm -rf /usr/bin/hexo
sudo ln -s /usr/local/node-v17.6.0-linux-x64/bin/hexo /usr/bin/hexo
hexo -v
cd /home
hexo init blog
cd /blog
npm install
hexo g
```



# 2. 配置github

新建仓库：
![在这里插入图片描述](https://s2.loli.net/2022/05/29/oziOZeLpI59hQPF.png)

假设我的用户名为 roomdestroyer ，邮箱为 roomdestroyer@gmail.com 按以下命令生成ssh：

```bash
git config --global user.name roomdestroyer 
git config --global user.email roomdestroyer@gmail.com
git config user.name
git config user.email
ssh-keygen -t rsa -C roomdestroyer@gmail.com
# 连续摁 3 个回车
cd /root/.ssh
cat id_rsa.pub
```
复制下来屏幕中的输出内容

回到 github, 依次点击：`头像 -> Settings -> SSH and GPG keys -> New SSH key -> 把刚刚复制的内容粘贴到 Key 处 -> Add SSH key` ： 

![在这里插入图片描述](https://s2.loli.net/2022/05/29/FjRrsv6fpy7h8JC.png)
回到 Shell，执行命令 `ssh -T git@github.com` 查看是否成功。

再回到 github 你刚刚创建的 blog 仓库，复制 SSH 地址：

![在这里插入图片描述](https://s2.loli.net/2022/05/29/2kEU1PLMObVnsJW.png)

再回到 Shell，执行以下命令：

```bash
cd /home/blog
vim _config.yml
```

按 `shift + G` 来到末尾，按如下配置：

```bash
deploy:
  type: 'git'
  repository: 你刚刚复制的地址
  branch: main
```

网上翻，找到 `URL` 配置模块，按如下配置：

```bash
url: https://github.com/你的用户名/
root: /blog.github.io
```

配置好后，按一下 `ESC`，执行 `:wq`，`Enter` ，来到 Shell 界面，依次执行以下命令：

```bash
cd /home/blog
hexo clean
hexo g
hexo d
```

此时会要求输入账号和凭证，账号写自己的账号，密码去 github 找 token，登录你的 github 后，访问该网址：`https://github.com/settings/tokens` ，在该界面点击 `Generate New Token` ，Note随便写一个，有效期随便选一个，下面一路打√，确认生成后，记得复制，后面就查不到了：

![在这里插入图片描述](https://s2.loli.net/2022/05/29/UM2miQWTHhObqwX.png)


然后回到你的 Shell，账号就填自己的号，密码填这个 token，等一会就部署上去了。


3. 生成页面：

部署成功后此时你的界面应该是这样的，按下面一路操作 ：
![在这里插入图片描述](https://s2.loli.net/2022/05/29/dlrOF4IYKA2i6gR.png)

![在这里插入图片描述](https://s2.loli.net/2022/05/29/DHEQF8jakTUOwPo.png)


![在这里插入图片描述](https://s2.loli.net/2022/05/29/xLjCovPM8Zqwgsp.png)

![在这里插入图片描述](https://s2.loli.net/2022/05/29/oYXtP7jf6sUu1vz.png)

![在这里插入图片描述](https://s2.loli.net/2022/05/29/t7IPR1maQdzqhjb.png)

![在这里插入图片描述](https://s2.loli.net/2022/05/29/OuaGidwqgxZ5f8Y.png)