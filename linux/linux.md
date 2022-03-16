# 环境变量

## 5个文件+1个方式

### profile、.bashrc

- 系统级
  - 电脑启动：执行/etc/environment
  - 用户登录成功后：执行/etc/profile
    - 该文件一般是调用/etc/bash.bashrc文件
  - 每次打开shell：/etc/bash.bashrc
    - 系统级的bashrc文件，为每一个运行bash shell的用户执行此文件。
    - 此文件会在用户每次打开shell时执行一次。

- 用户级
  - 登录成功：~/.profile
    - 默认情况下,~/.profile设置一些环境变量,并执行用户的.bashrc文件
  - 每次打开shell：~/.bashrc



那么根据以上描述，这几个文件的执行先后顺序应当是：
/etc/enviroment –>/etc/profile –>~/.profile –>/etc/bash.bashrc–> ~/.bashrc

> 我的台式机mint中，本来python是系统中的python2,而安装anaconda后，变成了anaconda的python，原因是anaconda是在~/.bashrc中改的

### rpm安装

在/usr/bin下建立一个软连接，就可以找到应用程序了

## 配置

- 仅使用一次
  - 设置：*export PYTHONPATH=/home/……*
  - 查看：*echo PYTHONPATH*
- 永久
  - *sudo gedit ~/.bashrc* + *source ~/.bashrc*
  - *sudo gedit /etc/profile + source /etc/profile*

- 修改方式

  ```
  #比如添加搜索路径/home/cc/path1和/home/cc/path2 路径到PATH中,采用:来分隔,冒号左右不需要空格
  
  export PATH=$PATH:/home/cc/path1:/home/cc/path2
  
  #若需要将路径放在优先搜索位置，将$PATH放在后面
  
  export PATH=/home/cc/path1:/home/cc/pck:$PATH
  ```

# 目录架构

**usr** 指 **Unix Software Resource**，/usr 目录包含所有的命令、程序库、文档和其它文件，我们可以命令到这个目录下，ls -al看下，都是系统预装的可执行程序，会随着系统的升级而改变，/usr/local/bin目录是给用户放置自己的可执行程序，所以我们一般把我们可执行的脚本文件放到这个目录，这样我们在linux系统里面任何目录都可以运行了。



`/usr`：系统级的目录，可以理解为`C:/Windows/`，`/usr/lib`理解为`C:/Windows/System32`。
`/usr/local`：用户级的程序目录，可以理解为`C:/Progrem Files/`。用户自己编译的软件默认会安装到这个目录下。
`/opt`：用户级的程序目录，可以理解为`D:/Software`，opt有可选的意思，这里可以用于放置第三方大型软件（或游戏），当你不需要时，直接`rm -rf`掉即可。在硬盘容量不够时，也可将/opt单独挂载到其他磁盘上使用。
`/usr/src`：系统级的源码目录。
`/usr/local/src`：用户级的源码目录

```shell
### /etc/environment中的内容

#正因为此，放置在/usr/bin和/usr/local/bin中的文件才可以被shell直接执行
PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin"
```

# Shell

## 链接

链接命令 ln
命令名称：ln
英文原意：link
命令所在路径：/bin/ln
执行权限：所有用户
语法：ln -s [原文件] [目标文件]
　　　-s参数： 创建软链接
功能描述：生成链接文件
示例：
ln -s /etc/issue /tmp/issue.soft ：创建/etc/issue文件的软链接/tmp/issue.soft
ln /etc/issue /tmp/issue.hard：创建/etc/issue文件的硬链接/tmp/issue.hard

# 创建软件图标

- 一般发行版桌面图标配置文件存放在**/usr/share/applications/**下，在这个目录下存放了很多.desktop为后缀的文件，这些文件就是桌面图标的配置文件。

  - 创建 xxx.desktop

    ```
    [Desktop Entry]
    Comment=xxx
    Name=xxx
    Exec=/opt/**/xxx
    Encodeding=UTF-8
    Terminal=false
    Type=Application
    Categories=Application;
    Icon=//picture_directory
    ```

    

# 截屏

https://blog.csdn.net/weixin_38389124/article/details/102477012

# 安装包

## 查看软件最初拿什么安装的

因为linux安装软件的方式比较多，所以没有一个通用的办法能查到某些软件是否安装了。总结起来就是这样几类：

1、rpm包安装的，可以用rpm -qa看到，如果要查找某软件包是否安装，用 rpm -qa | grep “软件或者包的名字”。

```
[root@hexuweb102 ~] rpm -qa | grep ruby
```

2、以deb包安装的，可以用dpkg -l能看到。如果是查找指定软件包，用dpkg -l | grep “软件或者包的名字”；

```
[root@hexuweb102~]dpkg-l|grepruby
```

3、yum方法安装的，可以用yum list installed查找，如果是查找指定包，命令后加 | grep “软件名或者包名”；

```
[root@hexuweb102 ~] yum list installed | grep ruby
```

4、如果是以源码包自己编译安装的，例如.tar.gz或者tar.bz2形式的，这个只能看可执行文件是否存在了，

上面两种方法都看不到这种源码形式安装的包。如果是以root用户安装的，可执行程序通常都在/sbin:/usr/bin目录下。

说明：**其中rpm yum 是Redhat系linux的软件包管理命令，dpkg是debian系列的软件包管理命令**
