# shell 脚本

## 传递参数

参数   | 描述
:--- | :------------------------------------------------------------------------
`$#` | 传递到脚本的参数个数
`$*` | 以一个单字符串显示所有向脚本传递的参数。如"$\*"用「"」括起来的情况、以"$1 $2 ... $n"的形式输出所有参数。
`$$` | 脚本运行的当前进程ID号
`$!` | 后台运行的最后一个进程的ID号
`$@` | 与$\*相同，但是使用时加引号，并在引号中返回每个参数。如"$@"用「"」括起来的情况、以"$1" "$2" ... "$n" 的形式输出所有参数。
`$-` | 显示Shell使用的当前选项，与set命令功能相同。
`$?` | 显示最后命令的退出状态。0表示没有错误，其他任何值表明有错误。

## getopts 与 shift

test.sh 脚本：
```bash
#!/bin/bash
# 第一个冒号表示忽略错误，字符后面没有冒号表示该选项必须有自己的参数
while getopts ":ab:c:" opt
do
    case $opt in
        a)
        echo a;;
        b)
        echo "b $OPTARG";;
        c)
        echo "c $OPTARG";;
        ?)
        echo "error"
        exit 1;;
    esac
done
```

执行结果：
```
$ test.sh -a -b hh  -c hh
a
b hh
c hh
```

如果要解析这样格式的：
```
test.sh rm -a -b hh -c hh
```

可以使用 shift 。

```bash
#!/bin/bash
echo before shift $#
# shift 1 会把第一个参数移除 ， shift 2 会把前两个参数移除
shift 1
echo after shift $#
# 第一个冒号表示忽略错误，字符后面没有冒号表示该选项必须有自己的参数
while getopts ":ab:c:" opt
do
    case $opt in
        a)
        echo a;;
        b)
        echo "b $OPTARG";;
        c)
        echo "c $OPTARG";;
        ?)
        echo "error"
        exit 1;;
    esac
done
```

执行结果：

```
$ test.sh rm -a -b hh  -c hh
before shift 1 6
after shift 1 5
a
b hh
c hh
```

## getopt

getopt 与 getopts 的区别：
> - getopts 是 shell 内建命令， getopt 是一个独立外部工具
> - getopts 使用语法简单，getopt 使用语法复杂
> - getopts 不支持长参数（长选项，如 --option）， getopt 支持getopts 不会重排所有参数的顺序，getopt会重排参数顺序 (getopts 的 shell 内置 OPTARG 这个变量，getopts 通过修改这个变量依次获取参数，而 getopt 必须使用 set 来重新设定位置参数，然后在 getopt 中使用 shift 来依次获取参数)
> - 如果某个参数中含有空格，那么这个参数就变成了多个参数。因此，基本上，如果参数中可能含有空格，那么必须用getopts(新版本的 getopt 也可以使用空格的参数，只是传参时，需要用 双引号 包起来)。

```sh
#!/bin/bash
#-o或--options选项后面接可接受的短选项，如ab:c::，表示可接受的短选项为-a -b -c，其中-a选项不接参数，-b选项后必须接参数，-c选项的参数为可选的
#-l或--long选项后面接可接受的长选项，用逗号分开，冒号的意义同短选项。
#-n选项后接选项解析错误时提示的脚本名字
ARGS=`getopt -o pcskxnj --long normal,parent,child,java,spring,kotlin,kotlinSpring -n 'gen-proj' -- "$@"`
if [ $? != 0 ]; then
    echo error
    exit 1
fi

#将规范化后的命令行参数分配至位置参数（$1,$2,...)
eval set -- "${ARGS}"

while true
do
  case "$1" in
    -n|--normal) shift;;
    -p|--parent) shift;;
    -c|--child) shift;;
    -j|--java) shift;;
    -s|--spring) shift;;
    -k|--kotlin) shift;;
    -x|--kotlinSpring) shift;;
    --)
      shift
      break
      ;;
    *)
      echo "Internal error!"
      showHelp
      exit 1
      ;;
  esac
done
```

**注意**

case 中解析完参数一定要执行 `shift` 否则会陷入死循环。

## 参考链接

- [[shell] 脚本之shift和getopts (转载)](https://www.cnblogs.com/hjfeng1988/p/6875201.html)
- [使用 getopt 处理命令行长参数（长选项）](https://blog.csdn.net/u011641885/article/details/47429273)
- [使用getopt命令解析shell脚本的命令行选项](https://blog.csdn.net/sofia1217/article/details/52244582)
- [详解Linux shell命令帮助格式](https://blog.csdn.net/littlewhite1989/article/details/54425071)
