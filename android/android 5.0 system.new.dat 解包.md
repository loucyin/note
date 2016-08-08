## Extraktor 解包
- 将卡刷包中的 system.new.dat 和 system.transfer.list 放入工具中 place_for_supported_files_here 文件夹内
- 打开工具中的Extraktor_EN.cmd
- 输入1，回车
- 稍作等待，直到出现Done，解包完成！工具中的extract_file_here文件夹即可查看
## sdat2img 解包
- Linux命令(仅支持64位操作系统)：sdat2img
命令实现如：
```
sdat2img system.transfer.list system.new.dat new_system.img
```
然后在挂载new_system.img即可：
```
sudo mkdir -p /mnt/rom
sudo mount -t ext4 -o loop,ro,noexec,noload new_system.img /mnt/rom
```
在/mnt/rom目录下即可看到system目录的内容，
- windows命令(支持32位/64位系统)：sdat2img.exe  
命令实现如：
```
sdat2img.exe system.transfer.list system.new.dat  
```
然后再用 ext4_unpacker.exe 打开 new_system.img 即可。无解，打不开 img 文件。
## 参考链接
- [Android5.0(Lollipop) system.new.dat解包方法](http://www.romzhijia.net/shuaji/13548)
- [一键解包工具Extraktor v2.1，已支持android 6.0](http://bbs.gfan.com/forum.php?mod=viewthread&tid=8101586&page=1)
