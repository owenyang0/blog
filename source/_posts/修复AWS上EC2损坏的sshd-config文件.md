title: 修复AWS上EC2损坏的sshd_config文件
date: 2015-03-16 20:11:09
tags: [linux, aws, ec2, ssh, passwd, amazon-web-services]
---
**常识：** AWS是没有root用户的，登陆也都是通过SSH KEY完成授权认证。

**背景：** 正在AWS上搭一个` CI ` (GO)，与` gitlab `，为了将其进行集成，需将gitlab的deploy key设置成GO的SSH KEY。然而，GO创建的是无密码的用户go，导致无法进入用户go的home目录。
正常 ` su go ` 无法切换到go用户，当时又恰巧正在看SSH的config文件：
```
/etc/ssh/sshd_config
```
里面有一条 `PermitEmptyPasswords no`，接着便私自改成了` yes `，无用。` PermitRootLogin no `，改成 ` yes `，依旧无用。
**注：**废话，肯定没用啊，这是设置SSH的。

正确的做法：
```bash
sudo su go
```

当修改之后，没改回来，当我退出AWS之后就杯具了。再也登陆到AWS上，因为登陆时却输密码，而实际却是没有密码。此时真想多天说一句：X。


Google了很多解决方案，都说得是用Live CD，重新引导进入，然后挂载有sshd_config文件的磁盘并修复它。这在EC2上是根本不可能的，能做的只有把root磁盘卷拆卸，再装载到另外的EC2实例上，并修改相应的文件。以下便是详细操作：

1. 关闭当前EC2实例
2. 将有错误sshd_config的磁盘(EBS)拆卸
3. 当磁盘重新装载到另一个EC2实例上，并挂载该磁盘
拆卸与装载都在AWS的console界面操作，装载成功后，可用以下命令挂载（我的新的磁盘名字是 xdf)：
```bash
sudo mkdir /mnt/other
sudo mount /dev/xvdf /mnt/other
```
4. 编辑损坏的文件sshd_config，修复配置与语法错误
5. 反挂载并拆卸掉该磁盘
```bash
sudo umount /mnt/other
```
6. 再次将该磁盘装载到原EC2实例上
7. 启动原EC2实例，测试修改结果

这些做完之后，一切都如以前一样。又可以通过SSH KEY连上原来的实例。

### 结论
教训是，永远别再一次做这种事。在适当的时候，也可以给你的编辑器sudo的权限，vim的配置
```
cnoremap w!! %!sudo tee > /dev/null %
```