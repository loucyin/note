# atom note

## 常用快捷键

Ctrl + t 查找所有文件<br>
Ctrl + p 查找所有命令<br>
Ctrl + \ 打开或关闭目录树<br>
Ctrl + r 文件内查找变量或者方法<br>
Ctrl + b 查找打开的文件<br>
Ctrl + Shift + b 查找上次提交后更改的文件

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
