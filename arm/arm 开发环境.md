# 搭建基于 Eclipse 的 arm 开发环境

## 安装 CDT

- 电脑已经安装 Eclipse J2EE 版本
- 下载，安装 CDT

## 安装 Toolchain

通过 apt-get 安装

- 安装 arm-gnu-toolchain

  - sudo add-apt-repository ppa:team-gcc-arm-embedded/ppa
  - sudo apt-get update
  - sudo apt-get install gcc-arm-none-eabi

下载编译安装，[地址](https://launchpad.net/gcc-arm-embedded)。

## 安装Jlink

[这里](https://www.segger.com/downloads/jlink?page_request=jlink-software.html),下载安装。

## 安装GNU ARM Eclipse插件包

- [下载](https://sourceforge.net/projects/gnuarmeclipse/files/latest/download) 手动安装
- 填写 url 自动安装 ： <http://gnuarmeclipse.sourceforge.net/updates>

## 安装 stlink

[下载源码](https://github.com/texane/stlink)，按照说明文档编译安装。<br>
几条命令：

```
arm-none-eabi-gdb
target remote:4242
load hello.elf
```

在 gdb 界面输入 c 运行程序。

## 安装 OpenOCD

- [下载地址](https://sourceforge.net/projects/openocd/),下载安装
- 编译安装

  ```
  ./configure --prefix=/usr
  make
  sudo make install
  ```

- 使用 openocd

  ```
  cd /usr/share/openocd/scripts/interface
  sudo openocd -f stlink-v2.cfg -f ../board/stm32f429discovery.cfg
  ```

## 参考链接

- [Eclipse + CDT + YAGARTO + J-Link,STM32开源开发环境搭建与调试](http://www.cnblogs.com/lidabo/p/4517030.html)
- [GNU ARM Embedded Toolchain](https://launchpad.net/~team-gcc-arm-embedded/+archive/ubuntu/ppa?field.series_filter=xenial)
- [Linux下STM32开发环境的搭建](http://www.cnblogs.com/amanlikethis/p/3803736.html#lab33)
- [JLink - JLink for Ubuntu](http://blog.csdn.net/iamlvshijie/article/details/8480892)
- [GNU ARM Embedded Toolchain](https://launchpad.net/gcc-arm-embedded)
- [J-Link / J-Trace Downloads](https://www.segger.com/downloads/jlink?page_request=jlink-software.html)
- [OpenOCD](http://www.openocd.net/)
- [Tutorial: Using Eclipse + ST-LINK/v2 + OpenOCD to debugh](http://community.particle.io/t/tutorial-using-eclipse-st-link-v2-openocd-to-debug/10042)
