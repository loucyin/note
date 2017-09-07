# apidoc
## apidoc 安装
### 安装 nodejs
[nodeJs 下载地址](http://nodejs.cn/download/)

### npm 配置淘宝镜像

```
npm config set registry https://registry.npm.taobao.org
```

### 安装 apidoc

```
npm install apidoc -g
```

### 使用 apidoc

```
apidoc -i E:\code -o E:\api
```

apidoc 详细使用说明，见[参考链接](http://apidocjs.com/)。

## apidoc 简介

### group and name

params|描述|默认
:---|:---|:---
@apiName|用于指定方法名|如 `@api {PUT} /boxes/config/password 修改盒子的访问密码`<br/>name 为：PutBoxesConfigPassword
@apiGroup|用于指定 api 所属分组|如果 group 中为 `boxes/config`<br/>url 中会转为 boxes_config

apiGroup + apiName 可以用于定位 api 在 html 中的位置，规则如下：

```
#api-{apiGroup}-{apiName}
```

### apiDescription
如果在描述中希望跳转到其他 api ，可以使用如下代码（前提是知道 api 的 group 与 name）：
```html
<a href="#api-group-name">group/name</a>
```

## 参考链接
- [apidoc-install](http://apidocjs.com/#install)
