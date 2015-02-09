title: NPM install - killed error solution
date: 2015-02-09 19:29:46
tags: [npm, npm install, killed, memory, npm install killed]
---

在接手一个Node项目的时候，`npm install`。却出现了”killed”的错误。以为是Node版本的问题，熟练地切换了0.11与0.10版，同样无解。
由于新的npm版本吧，`npm install`的时候已经无法看到常规的log信息了。以`npm install -d`查看安装的过程，发现无任何特殊的地方。在一定程度的时候，直接被”killed”。关于`npm install -d`可以看 [npm config](https://docs.npmjs.com/misc/config)
```
-v: --version
-h, -?, --help, -H: --usage
-s, --silent: --loglevel silent
-q, --quiet: --loglevel warn
-d: --loglevel info
-dd, --verbose: --loglevel verbose
-ddd: --loglevel silly
-g: --global
-C: --prefix
-l: --long
-m: --message
-p, --porcelain: --parseable
-reg: --registry
-f: --force
-desc: --description
-S: --save
-D: --save-dev
-O: --save-optional
-B: --save-bundle
-E: --save-exact
-y: --yes
-n: --yes false
ll and la commands: ls --long
```

最后才想到，可能是内存不够了。由于是以vagrant启动的一台ubuntu14.04的虚拟机，默认只有500M的内存。而且该项目会用到一些底层的npm包，会编译一些代码。这就导致了内存不够用。
可以查一下相应的系统log，在路径 `/var/log`之下，确认自己的问题。如`/var/log/syslog` 和 `/var/log/messages`等。
### Solution
#### 1. 增大内存。
由于我是虚拟机，所有增大内存相对简单。在vagrantfile里，只需要增加一条：
```
  config.vm.provider "virtualbox" do |v|
    v.memory = 1024
  end
```
即可。
#### 2. 利用swap file实现虚拟内存
使用
```
sudo swapon -s
```
查看系统是否配置了交换区域。没有的话， 便直接开始创建swap file。
创建一个1G的文件。
```
sudo fallocate -l 1G /swapfile
```
使用
```
ls -lh /swapfile
```
确认是否是自己所需要的文件大小。

**启用Swap文件**
首先调整文件的权限，若该文件能被其他用户随意的读写，则会产生很大的安全隐患。
```
sudo chmod 600 /swapfile
```
现在安全多了，可以让系统开始设置交换区域了。
```
sudo mkswap /swapfile
```
启用该文件
```
sudo swapon /swapfile
```
现在基本的步骤已经完成了，可以使用最初的命令验证swap file是否正确使用。
```
sudo swapon -s

Filename                    Type          Size     Used     Priority
/swapfile                               file          1048572     0     -1
```
交换分区已经成功设置，系统会在必要的时候使用它。

**注意**
现在已经启用了swap文件，但当我们重启的时候，系统不会自动地启用该swap文件。可以通过修改`fstab`文件实现开机使用swap文件。
用root权限编辑
```
sudo vim /etc/fstab
```
在文件的最后，需要添加一行告诉操作系统自动使用swap文件。
```
/swapfile   none    swap    sw    0   0
```

如此再`npm install` 的时候，便解决了所有的问题了。