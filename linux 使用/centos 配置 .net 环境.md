# Centos 配置 .net 环境

## 安装 mono

## 安装 jexus

Ubuntu

```
cd /tmp
wget linuxdot.net/down/jexus-5.8.2.tar.gz
tar -zxvf jexus-5.8.2.tar.gz
cd jexus-5.8.2
sudo ./install
```

Centos

```
cd /tmp
sudo /usr/jexus/jws stop
wget linuxdot.net/down/jexus-5.8.2.tar.gz
tar -zxvf jexus-5.8.2.tar.gz
cd jexus-5.8.2
sudo ./upgrade
```

## jexus 配置

配置文件路径

```
/usr/jexus/siteconf
```

网站配置的基本内容：

```
port=80                          # jexus WEB服务器侦听端口（必填。当然可以是其它端口）
root=/ /var/www/mysite           # 网站URL根路径（虚拟目录）和对应的物理路径，两个路径字串之间必须用空格分开（必填。既使这个网站是一个纯粹的反向代理站，也得填）
```

启动

```
cd /usr/jexus
./jws start
./jws stop
./jws restart
```

## 参考链接

- [jexus](http://www.jexus.org/)
- [32和64位的CentOS 6.0下 安装 Mono 2.10.8 和Jexus 5.0](http://www.cnblogs.com/shanyou/archive/2012/01/07/2315982.html)
- [在Ubuntu操作系统上安装mono的具体方法](https://www.linuxdot.net/bbsfile-3090)
