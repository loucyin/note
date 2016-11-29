# CentOS7 tomcat 开机自启动

## tomcat增加启动参数

tomcat 需要增加一个pid文件 在tomca/bin 目录下面，增加 setenv.sh 配置，catalina.sh启动的时候会调用，同时配置java内存参数。

```
#add tomcat pid
CATALINA_PID="$CATALINA_BASE/tomcat.pid"
#add java opts
JAVA_OPTS="-server -XX:PermSize=256M -XX:MaxPermSize=1024m -Xms512M -Xmx1024M -XX:MaxNewSize=256m"
```

## 增加 tomcat.service

在/usr/lib/systemd/system目录下增加tomcat.service，目录必须是绝对目录。

```
[Unit]
Description=Tomcat
After=syslog.target network.target remote-fs.target nss-lookup.target

[Service]
Type=forking
PIDFile=/data/tomcat/tomcat.pid
ExecStart=/data/tomcat/bin/startup.sh
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s QUIT $MAINPID
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

## 参考链接

- [CentOS7 增加tomcat 启动，停止，使用systemctl进行配置](http://linux.it.net.cn/CentOS/course/2015/0201/12774.html)
- [CentOS 7.x设置自定义开机启动,添加自定义系统服务](http://www.centoscn.com/CentOS/config/2015/0507/5374.html)
