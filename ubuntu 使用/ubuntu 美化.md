## 几个工具
### unity-tweak-tool
- unity  
可以在启动器中设置启动器的位置，启动器的图标大小，显示桌面图标等。
- 窗口管理  
可以设置热区

### gnome-tweak-tool
不知道怎么用

### gconf-editor
- 隐藏 docky 中的 docky 图标  
apps -> docky-2 -> Docky -> items -> DockyItem 取消勾选 ShowDockyItem 。  

## 安装字体
- copy windows 字体文件夹  
windows 下字体放在 C:\\Windows\\Fonts 文件夹下面；  
ubuntu 下对应这样的路径：/media/loucunyin/系统/Windows/Fonts；  
ubuntu 下字体文件的存放路径：/usr/share/fonts；
```bash
cd /media/loucunyin/系统/Windows
cp Fonts /usr/share/fonts
```
- 安装字体
```bash
cd /usr/share/fonts/Fonts
sudo mkfontscale
sudo mkfontdir
sudo fc-cache -fv
```

## 安装主题
[gnome 主题包下载地址](http://gnome-look.org/)  
- 主题包：
提取，然后复制到 /usr/share/themes/ 下面。
- 图标包：
提取，然后复制到 /usr/share/icons/ 下面。
- 通过 gnome-tweak-tool 或 unity-tweak-tool 设置图标或主题  

## 标题半透明
以缺省主题Ambiance为例，只需修改/usr/share/themes/Ambiance/gtk-3.0/gtk-main.css的一行代码，把：  
@define-color dark_bg_color #3c3b37;  
修改为：  
@define-color dark_bg_color rgba(60,59,55,0.4);
其中0.4是透明度。
## 禁用、启用触摸板
把设置里面的触摸板设置给弄没了。  
禁用：sudo rmmod psmouse   
启用：sudo modprobe psmouse   
