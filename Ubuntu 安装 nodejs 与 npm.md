# Ubuntu安装nodejs与npm

## 1.下载

打开淘宝镜像站，找到自己需要的nodejs版本，下载压缩文件。

网址：<a href="https://registry.npmmirror.com/binary.html?path=node/v17.6.0/">https://registry.npmmirror.com/binary.html?path=node/v17.6.0/</a>
例如，我服务器的环境是Ubuntu20.04，则选择压缩包 `node-v17.6.0-linux-x64.tar.gz` 下载



## 2. 上传

上传到Ubuntu环境中，我的目录是 /usr/local ，使用命令 `tar -xvzf node-v17.6.0-linux-x64.tar.gz`解压到目录中，使用命令 `cd /usr/local/node-v17.6.0-linux-x64/bin` 移动到nodejs的bin目录下，此时 node 和 npm 都在该文件中，使用 `./node -v` 或`./npm -v` 可查看 node 和 npm 的版本



## 3. 环境变量

为更方便使用命令 `node` 和 `npm` ，用软连接将命令添加到环境变量中：

```bash
sudo ln -s /usr/local/node-v17.6.0-linux-x64/bin/node /usr/bin/node
sudo ln -s /usr/local/node-v17.6.0-linux-x64/bin/npm /usr/bin/npm
```

此时再使用命令 `node` 和 `npm` 可以查看到node和npm的版本，安装成功，之后再卸载也很方便，只需删除安装包和软链接即可。

（注：使用npm下载的各种包都保存在路径 `/usr/local/node-v17.6.0-linux-x64/bin/` 下，如果发现下载某个包后无法使用，去到该目录下使用命令 `find ./ -name ***` 查看你安装的包的路径，然后将它添加到软链接即可正常使用）