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

## apidoc 规范

- 必须的信息

参数|所属 params|描述
:---|:---|
method|@api|请求方式
path|@api|地址
title|@api|导航中的方法名
name|@apiName|英文，大驼峰命名
version|@apiVersion|版本号
description|@apiDescription|描述
group|@apiGroup|英文，大驼峰命名

- apiDefine 使用大驼峰命名
- apiParam apiSuccess 中的 group 使用小写下划线命名，如：

  ```
  @apiParam (box_config) {String} [userName] 盒子的用户名
  ```

- apiParam apiSuccess 中用到的对象，对象类型使用小写下划线命名，对象的描述信息组为对象类型名

  ```
  @apiParam (channel_config) {point[]} [area] 检测区域，最少 3 个点，最多 16 个点
  @apiParam (point)  {int} x 点的 x 坐标
  @apiParam (point) {int} y 点的 y 坐标
  ```

- apiParam apiSuccess 中参数类型使用小写下划线命名
- apiParam apiSuccess 中数组类型使用类型+[] 命名

- 使用 get 方式请求的 api 提供 url 示例
- post、put、delete 一般不要使用 url 传参（可以使用 url 路径参数，业务简单参数少于 2 个可以考虑 url 传参），传递参数时描述中需要有数据的 Content-Type，并提供传入数据格式的示例，from 表单要提供表单示例

## 参考链接
- [apidoc-install](http://apidocjs.com/#install)
