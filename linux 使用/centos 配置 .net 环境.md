# Centos 配置 .net 环境

## 安装 mono

### 编译安装

- 安装依赖

  ```
  yum -y install gcc gcc-c++ bison pkgconfig glib2-devel gettext make libpng-devel libjpeg-devel libtiff-devel libexif-devel giflib-devel libX11-devel freetype-devel fontconfig-devel  cairo-devel
  ```

- 编译安装Mono需要的GDI+兼容API的库Libgdiplus

  ```
  cd /usr/local/src/
  wget http://download.mono-project.com/sources/libgdiplus/libgdiplus-2.10.tar.bz2
  tar -jxvf libgdiplus-2.10.tar.bz2
  cd libgdiplus-2.10
  ./configure --prefix=/usr
  make
  make install
  ```

  ```
  yum -y install libtool*
  git clone git://github.com/mono/libgdiplus.git
  cd libgdiplus
  ./autogen.sh --prefix=/usr
  make
  sudo make install
  ```

- 编译安装 mono

  ```
  cd /usr/local/src/
  wget http://download.mono-project.com/sources/mono/mono-2.10.8.tar.bz2
  tar -jxvf mono-2.10.8.tar.bz2
  cd mono-2.10.8
  ./configure --prefix=/usr
  make
  make install
  ```

- 安装后运行

  ```
  ldconfig
  ```

  > ldconfig命令的用途,主要是在默认搜寻目录(/lib和/usr/lib)以及动态库配置文件/etc/ld.so.conf内所列的目录下,搜索出可共享的动态链接库(格式如前介绍,lib_.so_),进而创建出动态装入程序(ld.so)所需的连接和缓存文件

### 官网方法

```
yum install yum-utils
rpm --import "http://keyserver.ubuntu.com/pks/lookup?op=get&search=0x3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF"
yum-config-manager --add-repo http://download.mono-project.com/repo/centos/
```

## 安装 jexus

- 安装

  ```
  cd /tmp
  wget linuxdot.net/down/jexus-5.8.2.tar.gz
  tar -zxvf jexus-5.8.2.tar.gz
  cd jexus-5.8.2
  sudo ./install
  ```

- 升级

  ```
  cd /tmp
  sudo /usr/jexus/jws stop
  wget linuxdot.net/down/jexus-5.8.2.tar.gz
  tar -zxvf jexus-5.8.2.tar.gz
  cd jexus-5.8.2
  sudo ./upgrade
  ```

## jexus 配置

- 配置文件路径

  ```
  /usr/jexus/siteconf
  ```

- 网站配置的基本内容：

  ```
  port=80                          # jexus WEB服务器侦听端口（必填。当然可以是其它端口）
  root=/ /var/www/mysite           # 网站URL根路径（虚拟目录）和对应的物理路径，两个路径字串之间必须用空格分开（必填。既使这个网站是一个纯粹的反向代理站，也得填）
  ```

- 启动

  ```
  cd /usr/jexus
  ./jws start
  ./jws stop
  ./jws restart
  ```

## windows to linux 大小写

> 首先正视Linux和win的一些区别，也就是一些常识，win的文件命名不区分大小写，而Linux区分大小写，所以创建文件的时候要注意大小写

编辑 /usr/jexus/jws

> 将#export MONO_IOMAP="all"前面的"#"去掉!

## 参考链接

- [jexus](http://www.jexus.org/)
- [32和64位的CentOS 6.0下 安装 Mono 2.10.8 和Jexus 5.0](http://www.cnblogs.com/shanyou/archive/2012/01/07/2315982.html)
- [在Ubuntu操作系统上安装mono的具体方法](https://www.linuxdot.net/bbsfile-3090)
- [centos 7下安装Mono、Jexus，遇到libgdiplus编译安装出错；点击查看解决方法](https://www.linuxdot.net/bbsfile-3751)
- [centos7+mono4.2.3.4+jexus5.8.1跨平台起飞](http://www.cnblogs.com/xpszy/p/5300297.html)
- [Install Mono on Linux](http://www.mono-project.com/docs/getting-started/install/linux/)
- [C#在Linux上的开发指南](http://www.cnblogs.com/RainbowInTheSky/p/5496777.html#commentform)
- [使用Jexus+Mono运行.net开发的项目如何不让URL区分大小写](http://blog.csdn.net/fwj380891124/article/details/50808754)
