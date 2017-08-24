# atom note

## 常用快捷键

Ctrl + t 查找所有文件<br>
Ctrl + p 查找所有命令<br>
Ctrl + \ 打开或关闭目录树<br>
Ctrl + r 文件内查找变量或者方法<br>
Ctrl + b 查找打开的文件<br>
Ctrl + Shift + b 查找上次提交后更改的文件

## 代码模板

- 搜索命令：Application:Open Your Snippets
- 回车打开 snippets 模板
- 编辑模板

  ```javascript
  '.source.coffee':
   'Console log':
     'prefix': 'log'
     'body': 'console.log $1'
  ```

  名称|内容
  :---|:---
  `.source.coffee`|模板使用类型(可以修改)
  `Console log`|模板名称(自定义)
  `'prefix': 'log'`|prefix 代码前缀，(自定义)<br>在相应的文件中，输入 `log` 会出现代码提示
  `'body': 'console.log $1'`|模板内容<br>选择模板后，模板代码会自动插入

  body 光标规则：

  名称|内容
  :---|:---
  ${1}|插入代码后，光标会出现在 ${1} 位置，回车后光标会跳到 ${2} 的位置
  ${2:接口名}|${2} 处的默认值为：接口名

## atom 安装插件

- 在 atom 中搜索插件, install；
- 使用 apm install 安装；
- 从 git 下载源码，拖到 user.atom\packages 下面，在当前 package 下面运行命令:

  ```
  apm install
  ```

  第一种和第二种安装太慢，而且没法看进度，第三种方法比较快

  ```bash
  cd ~/.atom/packages
  git clone https://github.com/zenorocha/atom-javascript-snippets.git
  cd atom-javascript-snippets
  apm install
  ```

## npm 设置镜像

npm 设置为淘宝镜像

```
npm config set registry https://registry.npm.taobao.org
```

## atom plugin

### common

- atom-beautify
- git-plus
- pretty-json
- script
- minimap
- file-icons

### python

- autocomplete-python
- linter-python-pep8
- python-indent
- python-tools

### web

- emmet-atom
- javascript-snippets
- atom-ctags
- atom-jshint
- csslint
- linter-csslint
- jslint

### java

- autocomplete-java

### swift

- autocomplete-swift
- language-swift
- swift-debugger

### markdown

- tidy-markdown
- markdown-scroll-sync
- markdown-writer
- markdown-preview-plus

## 参考链接

- [npm 如何设置镜像站](http://flytosea.iteye.com/blog/2092336)
- [淘宝 NPM 镜像](https://npm.taobao.org/)
