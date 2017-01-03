# bat 基本语法

## 变量

```
set str1=This is string1
```

## 使用变量

```
echo str1=%str1%
```

## 拼接字符串

```
set str1=This is string1  
set str2=This is string2

set str3==%str1%%str2%
```

## 截取字符串

用法 ：

> - set 目标字符串=%源字符串:~起始位置,长度（为负数时表示相对字符串结束的位置）%
> - 如果省略逗号和截取长度，将会从起始值一直截取到字符串的结尾。

```
@echo off
set ifo=abcdefghijklmnopqrstuvwxyz0123456789
echo 原字符串（第二行为各字符的序号）：
echo 123456789012345678901234567890123456
echo %ifo%
echo 截取前5个字符：
echo %ifo:~0,5%
echo 截取最后5个字符：
echo %ifo:~-5%
echo 截取第一个到倒数第6个字符：
echo %ifo:~0,-5%
echo 从第4个字符开始，截取5个字符：
echo %ifo:~3,5%
echo 从倒数第14个字符开始，截取5个字符：
echo %ifo:~-14,5%
pause
```

## 替换字符串

```
set aa=伟大的中国！我为你自豪！
set aa=%aa:中国=中华人民共和国%
```

## 参数传递

- 新建 test.bat

  ```
  @echo off
  set a=%1
  set b=%2
  echo first=%a%
  echo second=%b%
  pause
  ```

- 运行 test.bat a.txt b.txt

- 输出

  ```
  first=a.txt
  second=b.txt
  ```

## if

bat 的语法要求比较严格，关键字符必须和上一条条件在同一行，才能保证正确执行。

### 比较运算符

- EQU - 等于
- NEQ - 不等于
- LSS - 小于
- LEQ - 小于或等于
- GTR - 大于
- GEQ - 大于或等于

### 文件是否存在 exit

```
@echo off
if exist StringOpration.bat do
echo StringOpration exist
pause
```

### 条件后执行多条命令

```
if exist 1.txt (  
    echo yes  
    echo 1  
    echo 2  
    echo 3) else (  
    echo no  
)
```

### 条件取反 not

```
if not exist hh.txt echo hh.txt not exit
```

### 结合 errorlevel 使用

```
@ECHO OFF  
XCOPY F:\test.bat D:\  
IF ERRORLEVEL 1 ECHO 文件拷贝失败  
IF ERRORLEVEL 0 ECHO 成功拷贝文件
```

## for

格式：

```
格式：FOR [参数] %%变量名 IN (相关文件或命令)    DO 执行的命令
```

for命令可以带参数或不带参数，带参数时支持以下参数:/d /l /r /f

- ### 无参数

  ```
  @echo off  
  setlocal enabledelayedexpansion  
  set /a v=0  
  for %%i in (*.*) do (  
  echo %%i  
  echo -------!v!------  
  set /a v+=1  
  )  
  pause
  ```

  > setlocal enabledelayedexpansion : 表示开启变量延迟，可以监测for循环中变量的动态变化；

- ### /d

  参数只能显示当前目录下的目录名字

- ### /r

  搜索指定路径及所有子目录中与set相符合的所有文件

- ### /l

  表示以增量形式从开始到结束的一个数字序列。可以使用负的 Step

  ```
  for /l %%i in (1,1,5) do @echo %%i   --输出1 2 3 4 5    
  for /l %%i in (1,2,10) do @echo %%i   --输出1,3，5,7，9    
  for /l %%i in (100,-20,1) do @echo %%i   --输出100,80,60,40,20    
  for /l %%i in (1,1,5) do start cmd   --打开5个CMD窗口    
  for /l %%i in (1,1,5) do md %%i   --建立从1~5共5个文件夹    
  for /l %%i in (1,1,5) do rd /q %%i   --删除从1~5共5个文件夹
  ```

- ### /f

  ```
  for /f "skip=1 tokens=1,2-5 delims=,./ " %%a in ("1,2.10 11/12") do echo %%a %%b %%c %%d %%e
  ```

  #### skip

  for循环文本内容是以行为单位,从上至下进行的,skip=1意识就是跳过文本的第一行,即不循环第一行 那么skip=2 自然就是跳过前两行了,依次类推.........

  #### tokens

  - `-` : 代表范围，
  - `,` : 代表单一选项

  #### delims

  分隔符，空格作为分隔符时必须放到最后

  将文本每一行的内容以delims=后面的字符分割成若干列.

## 重命名

ren oldName newName

## 移动 & 复制

```
xcopy hh hhh/
```

复制 hh 为 hhh

```
copy /b 1.txt+2.txt 3.txt
```

将 1.txt 2.txt 合并复制到 3.txt

## atom 插件安装

--------------------------------------------------------------------------------

atom 下安装插件比较慢，在 github 上下载压缩包，使用 npm 进行安装。

--------------------------------------------------------------------------------

### 去掉当前文件夹下文件夹的 -master 后缀

解压后文件夹有 -master 后缀

```
@echo off   
setlocal enabledelayedexpansion
for /d %%i in (*-master) do (  
set oldName=%%i
set "newName=!oldName:~0,-7!"
echo newName:!newName!
ren %%i !newName!
)  
pause
```

### 批量安装 atom 插件

```
@echo off   
set curDir=C:\Users\Administrator\.atom\packages
for /d %%i in (*) do (  
cd %curDir%
cd %%i
npm install
apm link  
echo %curDir%%%i has installed
)  
pause
```

## 参考连接

- [批处理之字符串处理总结](http://blog.csdn.net/benkaoya/article/details/7784013)
- [BAT批处理中的字符串处理详解(字符串截取)](http://www.jb51.net/article/52744.htm)
- [bat批处理 if 命令示例详解](http://www.jb51.net/article/14986.htm)
- [浅析windows bat中IF语句的语法](http://www.285868.com/a/xtjc/5827.html)
- [Windows的bat脚本中for循环](http://123304258.blog.163.com/blog/static/12354702012621103256608/)
- [windows bat脚本for循环中对变量循环赋值](http://blog.csdn.net/u010161379/article/details/50956652)
- [[DOS批处理]for 命令 /f 参数 通俗讲解(转发)](http://blog.chinaunix.net/uid-10697776-id-2935612.html)
- [windows下Bat命令学习](http://blog.csdn.net/steven6977/article/details/10823165)
- [批处理(bat)脚本语言(4) - FOR循环](http://blog.csdn.net/fw0124/article/details/39996509)
- [批处理中Copy与Xcopy命令的区别小结](http://www.jb51.net/article/49585.htm)
