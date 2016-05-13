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
