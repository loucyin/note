## 背景
最近系统老提示升级，鉴于好久没刷过机了，刷一次吧，顺便记录一下过程。之前刷机刷的截图没法用了，升级完系统之后，截图依旧没法用，索性线刷一遍清理一下手机。由于手里只有 5.02 版本的线刷包，只好先刷入 5.02 在升级到 5.5.013S。

## 线刷  
### 线刷步骤
- 导出联系人，照片等重要内容
- 打开 flash_tool.exe，下载地址在参考链接中找
- 下载选项卡配置  
下载 DA ：flash_tool.exe 同目录下的 MTK_AllInOne_DA.bin；  
配置文件：刷机包中的 MT6795_Android_scatter.txt；   
选择下载（不能使用全部格式化和下载，会刷丢IMEI）。
- 安装驱动
- 关机
- 点击 flash_tool 中的下载按钮。
- 按住开机键，开始刷入，开始刷入后就可以松开开机键了。

### 参考链接
- [乐视手机 1 X600 救砖头完整线刷官方 ROM](http://bbs.ydss.cn/thread-576945-1-1.html)
- [驱动安装](http://bbs.ydss.cn/thread-575045-1-1.html)
- [移动叔叔MTK刷机驱动 v1.0.14.exe 密码：f84q](http://pan.baidu.com/s/1kTrMkXT)
- [刷机工具：移动叔叔刷机工具 SP_Flash_Tool_v5.1524（150817）.7z 密码：cdod](http://pan.baidu.com/s/1sjqocqx)

## 升级到最新版本
- 下载新版 rom [Letv-x600 官方 rom 下载地址](http://bbs.le.com/zt/eui/le_1.html);  
- 复制到内存卡根目录下，重命名为 update.zip
- 关机，同时按住 `开机键和音量 + 键` ，进入 recovery 模式
- 使用 update.zip 升级系统。
 
## 刷入 TWRP ，获取 root 权限
### 启用开发者选项
设置 -> 关于手机 -> 版本号 连续点击 7 次；  
返回设置，即可看到开发者选项；  
进入开发者选项，启用 USB 调试功能。
### 获取 root 权限
下载[Letv-x600-TWRP-Recovery-2.8.7.0](Letv-x600-TWRP-Recovery-2.8.7.0)；  
解压，运行 recovery.bat ，最后会进入 TWRP 的 recovery 模式；  
下载 [SuperSU](https://download.chainfire.eu/960/SuperSU/BETA-SuperSU-v2.72-20160510112018.zip) ，复制到手机中；  
在 TWRP 的 recovery 中安装更新，找到 SuperSU  刷入。

## 删除乐见
- 下载 re文件浏览器;
- 找到 /system/priv-app/Launcher3，解压 Launcher3.apk;  
使用 re 浏览器解压后的目录为：/storage/emulated/0/SpeedSoftware/Extracted/Launcher3；
- 切换到解压后的 Launcher3 目录  
在文件夹：/storage/emulated/0/SpeedSoftware/Extracted/Launcher3/assets/plugins 下会发现 lejian.apk 删除；  
- 压缩 Launcher3 文件夹  
利用 re 浏览器压缩后的文件路径为：/storage/emulated/0/SpeedSoftware/Archives/Launcher3.zip；
- 重命名 Launcher3.zip 为 Launcher3.apk
- 替换 /system/priv-app/Launcher3 下的 Launcher3.apk（替换前先备份一下，免得失败后都没法恢复）
- 进入 /data/data/com.android.Launcher3 文件夹  
删除 files 文件夹。
- 重启  
你就会发现漂亮的乐见消失了，再也不用担心误点走流量了。

## 精简系统应用
在 /system/priv-app 和 /system/app 文件夹下删除不想使用的应用，重启就会从桌面消失；  
重要的话说三遍：  
**除非你知道这些应用是干什么的，否则不要乱删，删成砖概不负责**  
**除非你知道这些应用是干什么的，否则不要乱删，删成砖概不负责**  
**除非你知道这些应用是干什么的，否则不要乱删，删成砖概不负责**  
