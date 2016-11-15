## 下载 git 源码，并解压
[git 压缩包](https://github.com/git/git)
## 根据 git 安装教程安装
```
$ make prefix=/usr all doc info ;# as yourself  
# make prefix=/usr install install-doc install-html install-info ;# as root
```
[git 官方给的安装指导](https://github.com/git/git/blob/master/INSTALL#L172)
1. 错误
```
git-compat-util.h:280:25: fatal error: openssl/ssl.h: 没有那个文件或目录  
```
```
sudo apt-get install  libssl-dev
```
- 错误  
```
http.h:6:23: fatal error: curl/curl.h: 没有那个文件或目录
```
```
sudo apt-get install libcurl4-openssl-dev
```
- 错误
```
/bin/sh: 2: asciidoc: not found  
make[1]: *** [git-add.html] 错误 127
make[1]:正在离开目录 `/home/loucunyin/git-master/Documentation'
make: *** [doc] 错误 2
```
```
sudo apt-get install asciidoc
```
[StackOverfolow 上关于安装 git 失败的回答 ](http://stackoverflow.com/questions/15197797/upgrade-git-to-1-8-1-5-failed)  
- 错误
```
/bin/sh: 2: docbook2x-texi: not found  
make[1]: *** [user-manual.texi] 错误 127
make[1]:正在离开目录 `/home/loucunyin/git-master/Documentation'
make: *** [info] 错误 2
```
```
sudo apt-get install docbook2x
```

- 错误
```
Fatal error: expat.h: No such file or directory
```
```
sudo apt-get install libexpat1-dev
```

## 之前安装不完全的原因
之前使用如下命令安装，忽略掉了 make 时的错误，导致安装不完全。
```
$ make configure ;# as yourself
$ ./configure --prefix=/usr ;# as yourself
$ make all doc ;# as yourself
# make install install-doc install-html;# as root
```
由于依赖的包不全，而导致安装不完整。
1. 现象
```
git clone https://github.com/git/git.git
```
执行上面的命令失败，但可以使用下面的命令代替。
```
git clone git://github.com/git/git.git
```
原因：缺少错误 1 中对应的包。
- 现象  
git 帮助不全面，只显示简单的帮助内容。
原因：缺少 asciidoc 依赖。

## 参考链接
- [Fatal error: expat.h: No such file or directory](https://github.com/scottcorgan/bucket-list/issues/2)
