# linux 下串口编程

## 无处不在的 nodejs

- 安装 nodejs

  ```
  sudo apt-get install nodejs
  ```

- 安装 serialport

  ```
  sudo apt-get install npm
  sudo ln -s /usr/bin/nodejs /usr/bin/node
  npm install serialport
  ```

  Ubuntu 下有一个名叫 node 的库，因此 nodejs 在 Ubuntu 下默认叫 nodejs 。 npm 在安装 serialport 时会报错找不到 node 。

## nodejs 的简单使用

- 命令行打印 hello nodejs 使用 nodejs 命令进入 nodejs 命令行：

  ```javascript
  console.log("hello nodejs");
  ```

- 文件打印 hello nodejs 新建文件 hello.js ，输入上面的代码，运行：

  ```
  nodejs hello.js
  ```

## 操作串口

## 参考链接

- [node-serialport](https://github.com/EmergingTechnologyAdvisors/node-serialport)
- [Ubuntu下提示/usr/bin/env: node: 没有那个文件或目录](http://blog.csdn.net/yypsober/article/details/51906691)
- [node-webkit：开发桌面+WEB混合型应用的神器](http://damoqiongqiu.iteye.com/blog/2010720)
