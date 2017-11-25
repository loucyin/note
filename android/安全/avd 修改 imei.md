# avd 修改 imei

## Android Sdk 中的模拟器

修改 emulator 的二进制文件，修改 IMEI

- emulator 路径 ：Sdk/emulator/emulator-x86.exe
- 通过 hexEdit  打开二进制文件
- 修改方法：查找 `+CGSN` ，后面的 15 个数字就是 IMEI

**没啥作用**

[HEXEdit 下载地址](http://www.hexedit.com/#)

## genymotion

通过修改 vbox 配置文件的方法修改 IMEI 不顶用。

YouTube 上的一个方法：

- 安装 Re 文件浏览器
- 编辑 /system/etc 下的 init.androVM.sh
- 找到相关内容，修改如下：
```
# Setting Device Id system properties from VirtualBox properties
prop_device_id=$(/system/bin/androVM-prop get genymotion_device_id)
if [ $? -ne 0 ]; then
  # Default value if unset
  setprop genyd.device.id "869654011122554"
else
  # Set user defined value. "[none]" keyword means empty value
  setprop genyd.device.id "869654011122554"
fi
```
869654011122554 即为修改后的 IMEI。

- 修改后重启虚拟机，IMEI 修改完成

## 参考链接
- [Device identifier of Android emulator](https://stackoverflow.com/questions/4402262/device-identifier-of-android-emulator)
