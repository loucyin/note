想学习一下 Linux ，记录一下安装、使用过程中遇到的坑。
## 装系统
- 下载系统镜像，解压到 U 盘。
- 进入引导界面，将 U 盘启动，设置为第一启动项。  
- 通过 U 盘启动系统。  
电脑安装过 win10 系统了，通过 U 盘启动老是无法进入安装界面，中途卡死，一次又一次的强关，能不能进入安装界面全看运气。最后发现，需要先把 win10 的引导关闭，`只留 U 盘引导`。  

## 系统环境
- 操作系统  
elementary OS 0.3.2 Freya (64-bit) 基于 Ubuntu 14.04 构建
- jdk 版本  
jdk-8u92-linux-x64.tar.gz
- eclipse 版本  
eclipse-jee-neon-M6-linux-gtk-x86_64

## 安装并配置 jdk
- 下载 jdk 安装包：jdk-8u92-linux-x64.tar.gz
- 解压
```
sudo tar zxvf jdk-8u92-linux-x64.tar.gz -C /home/username/
```
- 换个路径
```
 sudo cp -r  jdk1.8.0_92/  /opt/
```
- 设置环境变量  
方法有多种，另作笔记，下面为一种方式。  
打开用户目录下的 .bashrc 文件：sudo gedit ~/.bashrc ，在文件最后加入：
```
export JAVA_HOME=/opt/jdk1.8.0_92
export JRE_HOME=${JAVA_HOME}/jre  
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib  
export PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$JAVA_HOME:$PATH
```

## 安装 eclipse  
ubuntu 下可以通过软件中心安装 eclipse ，但是版本太旧，不想用。在官网下个包安装吧，却发现满满的全是坑。  
### 下载 eclipse 并解包  
### 坑，启动不了  
切到 eclipse 目录，执行 eclipse 文件。
```
cd /home/username/eclipse
./eclipse
```
经历漫长的等待，电脑卡死了，这是什么情况！！！
难道是版本问题，我下的是 J2EE 版本的，换成 J2SE 版本的也不行。  
各种 google 百度，最后发现都不好使。看到有人说用 sudo 启动，试一下吧。
```
sudo ./eclipse
```
毛线啊！当前路径下没有发现 Java 虚拟机，那就给你指明 VM 的位置。
```
sudo ./eclipse -vm /opt/jdk1.8.0_92/bin/java
```
哇喔，绕了一大圈，终于打开了，新建一个 Demo 吧，可以用了。  
### 坑，没有权限  
退出，看看不用 sudo 能不能打开。发现，打不开，没有一个文件的操作权限。那就改改权限呗。
```
sudo chmod -R 755 ./
```
发现这条指令，并没有什么用，带锁的依旧带锁，不带的依旧不带。Why ？？？ 看一下文件权限。
```
ls -l
```
原来文件所属用户为 root ,组也为 root，那就改一下所属用户和组。
```
sudo chown -R username ./
sudo chgrp -R username ./
```
再打开一次，竟然打开了，不过刚才建的 Demo 没法改，提示权限问题，关了，改一下 workspace 的权限。
### 又一个坑，关不掉  
点击关闭按钮后，竟然卡注销了！改了 Demo 的权限，重新进入，各种卡，而且界面显示也不正常，应该是权限的问题。
尝试着去更改权限，未果。
### 妥协了，sudo 就 sudo 吧  
不能一次一次敲指令打开吧！建个 shell ： eclipse.sh
```
#!/bin/bash
sudo /home/username/eclipse/eclipse -vm /opt/jdk1.8.0_92/bin/java
```
为啥单击不执行，那就通过命令执行，切到当前目录。
```
bash ./eclipse.sh
```
单击不执行，是文件权限问题，默认的新建文件没有添加执行权限，添加权限即可解决。打开了提示输密码，不方便。搜索到如下解决方案。
```
#!/bin/bash
echo 'password'|sudo -S /home/username/eclipse/eclipse -vm /opt/jdk1.8.0_92/bin/java
```
这样就连密码都省去输了。
### 桌面快捷方式  
新建一个文件： /usr/share/applications/eclipse.desktop ，输入下面的内容。
```
[Desktop Entry]
Encoding=UTF-8
Name=Eclipse
Comment=Eclipse IDE
Exec=/home/username/eclipse/eclipse.sh
Icon=/home/username/eclipse/icon.xpm
Terminal=false
Type=Application
Categories=GNOME;Application;Development;
StartupNotify=true
```
应用程序中出现 eclipse 的图标，单击可执行，总算搞定了。
### 慢着，怎么不能使用中文输入法
使用 sudo 打开 eclipse 无法使用中文输入法。  
接着更改用户和组吧，首先是 eclipse 目录，还有 workspace 目录。切换到两个目录，执行下面的命令。
```
sudo chown -R username ./
sudo chgrp -R username ./
```
执行玩之后打开 eclipse。   
发现可以使用中文输入法，貌似没有以前的问题了，神奇的事情。  
好像之前是没有更改过 workspace 的所属用户和组。

## 关于 eclipse preference 显示不正常的问题
在 eclipse.ini 文件中添加 GTK 版本：
在 `--launcher.appendVmargs` 下面添加：
```
--launcher.GTK_version
2
```

## 参考链接
- [Eclipse not working in 16.04](http://askubuntu.com/questions/761604/eclipse-not-working-in-16-04)
