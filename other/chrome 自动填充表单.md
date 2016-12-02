# chrome 自动填充表单

## 背景

前两天申请了一个账号，密码忘了，但是密码可以通过密保问题找回，可以密保问题的答案也忘了。chrome
密码还保存的不对，咋办呢，在申请个账号吧，填表的时候发现，表单也有历史记录，哇哦，这样的话我就可
以找回密保问题了。

## chrome 数据存在哪
- Windows 7-10：
```
C:\Users\<username>\AppData\Local\Google\Chrome\User Data\Default
```
- Mac OS X El Capitan：
```
Users/<username>/Library/Application Support/Google/Chrome/Default
```
- Linux：
```
/home/<username>/.config/google-chrome/default
```

chrome 使用 sqlite 存储表单数据，在 default 文件夹下，有一个 Web Data 文件，就是 sqlite 数据文件。  
通过 sqlite3 打开 Web Date 文件：  
select * from autofill ;  
哇偶，我的密保问题答案找回来了。
## 参考链接
- [Google Chrome view saved form data](http://superuser.com/questions/224261/google-chrome-view-saved-form-data)
- [如何快速找到Chrome配置文件路径](https://www.sysgeek.cn/find-chrome-profile-folder/)
