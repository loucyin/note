## 软件包格式
- deb格式  
可以直接点击安装，命令行：sudo dpkg -i xxxx.deb  
有些安装包，依赖关系不存在时，可以使用：sudo apt-get -f install   
- rpm格式
需要通过alien转换  
安装alien: sudo apt-get install alien  
转换为deb:sudo alien xxxx.rpm  
安装deb: sudo dpkg -i xxxx.deb  
