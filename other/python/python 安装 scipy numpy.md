# python 安装 scipy numpy

## 安装 scipy

### 错误

```
python not found in the registry
```

### 解决方法

```python
#
# script to register Python 2.0 or later for use with win32all
# and other extensions that require Python registry settings
#
# written by Joakim Loew for Secret Labs AB / PythonWare
#
# source:
# http://www.pythonware.com/products/works/articles/regpy20.htm
#
# modified by Valentine Gogichashvili as described in http://www.mail-archive.com/distutils-sig@python.org/msg10512.html

import sys

from _winreg import *

# tweak as necessary
version = sys.version[:3]
installpath = sys.prefix

regpath = "SOFTWARE\\Python\\Pythoncore\\%s\\" % (version)
installkey = "InstallPath"
pythonkey = "PythonPath"
pythonpath = "%s;%s\\Lib\\;%s\\DLLs\\" % (
    installpath, installpath, installpath
)

def RegisterPy():
    try:
        reg = OpenKey(HKEY_CURRENT_USER, regpath)
    except EnvironmentError as e:
        try:
            reg = CreateKey(HKEY_CURRENT_USER, regpath)
            SetValue(reg, installkey, REG_SZ, installpath)
            SetValue(reg, pythonkey, REG_SZ, pythonpath)
            CloseKey(reg)
        except:
            print "*** Unable to register!"
            return
        print "--- Python", version, "is now registered!"
        return
    if (QueryValue(reg, installkey) == installpath and
        QueryValue(reg, pythonkey) == pythonpath):
        CloseKey(reg)
        print "=== Python", version, "is already registered!"
        return
    CloseKey(reg)
    print "*** Unable to register!"
    print "*** You probably have another Python installation!"

if __name__ == "__main__":
    RegisterPy()
```

写入文件，运行，显示 python 2.7 is already registered ，安装 scipy 即可成功。

## 安装 numpy matplotlib

使用 pip 命令进行安装

```
pip install numpy
```

> For standard Python installations you will also need to install compatible versions of setuptools, numpy, python-dateutil, pytz, pyparsing, and cycler in addition to matplotlib.

安装 matplotlib 还需要安装上面的其他库，仍旧使用 pip 命令进行安装

## python 镜像设置

在unix和macos，配置文件为：$HOME/.pip/pip.conf
在windows上，配置文件为：%HOME%\pip\pip.ini
修改 ~/.pip/pip.conf (没有就创建一个)， 修改 index-url至tuna，例如

```
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
```

## 参考链接
- [matplotlib installing](http://matplotlib.org/users/installing.html)
- [python version 2.7 required,which was not found in the registry](http://www.cnblogs.com/thinksasa/archive/2013/08/26/3283695.html)
- [pypi 镜像使用帮助](https://mirrors.tuna.tsinghua.edu.cn/help/pypi/)
- [使用国内镜像源来加速python pypi包的安装](http://topmanopensource.iteye.com/blog/2004853)
- [Pipy pip源镜像，官网被墙时使用](http://www.tuicool.com/articles/3MJZBb)
- [pip user guide](https://pip.pypa.io/en/latest/user_guide/#config-file)
- [“/usr/bin/ld: cannot find -lz”](http://stackoverflow.com/questions/3373995/usr-bin-ld-cannot-find-lz)
- [Python-Matplotlib安装及简单使用](http://www.open-open.com/lib/view/open1393488232380.html)
