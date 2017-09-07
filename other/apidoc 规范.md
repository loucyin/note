# apiDoc 规则

## apiDoc 必须信息

参数|apiDoc-Params|描述
:---|:---|:---
method|@api|请求方式
path|@api|地址
title|@api|导航中的方法名
name|@apiName|英文
version|@apiVersion|版本号，版本号从 0.0.1 开始
description|@apiDescription|描述，需要对输入输出做简要说明
group|@apiGroup|英文

## 参数定义

参数类型|描述|注释方式|备注
:---|:---|:---|
PathParam | url 中 `/` 后以 `:` 开头表示路径参数，在请求时需要使用实际数据替换 | 使用 @apiParam 注释 group 为 **路径参数**|路径参数为必选参数
QueryParam |GET 方法传参方式| 使用 @apiParma 注释 group 为 **查询参数**|如果参数可选需要进行标记
BodyParam|POST PUT DELETE 传参方式|使用 @apiParma 注释 group 为 **内容参数**|如果参数可选需要进行标记<br>如果 BodyParam 中有嵌套数据类型，使用 @apiParma 加 group 定义
Response|响应数据|使用 @apiSuccess 注释 group 为 **返回数据**|如果参数可能为空需要进行标记<br>如果 Response 中有嵌套数据类型，使用 @apiSuccess 加 group 定义
ErrorCode|响应数据中可能出现的错误|使用 @apiError 注释 group 为 **错误码**|

示例：

```
/**
 * @api {get} /users/:id/orders 订单列表
 * @apiName OrderList
 * @apiVersion 0.0.1
 * @apiDescription 获取用户订单列表
 * @apiGroup UserInfo
 * @apiParam (路径参数) {Int} id 用户 id
 * @apiParam (查询参数) {Int} [start] 分页参数
 * @apiParam (查询参数) {Int} [limit] 分页参数
 * @apiSuccess (返回数据) {OrderInfo[]} restbody 订单列表
 * @apiSuccess (OrderInfo) {Int} id 订单 id
 * @apiSuccess (OrderInfo) {Date} createTime 订单生成时间
 * @apiSuccess (OrderInfo) {String} [comment] 评价
 * @apiError (错误码) {Int} 1001 授权失败
 * @apiError (错误码) {Int} 1002 用户不存在
 */
```

## Example(可选)

- GET 请求可以提供 url 示例，type 为 url ，title 为 **URL 示例**

  ```
  * @apiParamExample {url} URL 示例
  * /users/1/orders
  ```

- POST PUT DELETE 请求可以提供传入参数示例，title 为 **参数示例**，内容为格式化之后的 json 数据

  ```
  * @apiParamExample {json} 参数示例
  * {
  * "name": "张三",
  * "age": 12,
  * "gender": 1
  * }
  ```

- 返回数据，title 为 **结果示例**，内容为格式化之后的 json 数据

  ```
  * @apiSuccessExample {json} 结果示例
  * {
  * "resthead": {
  * "errorCode": 0,
  * "message": "OK"
  * },
  * "restbody": [
  * {
  * "id": 1,
  * "name": "保安1",
  * "phone": "18012345678",
  * "groupId": 1,
  * "addTime": "2017-06-28 10:57:52",
  * "updateTime": "2017-06-28 10:57:52",
  * "groupName": "小区1"
  * }
  * ],
  * "extend": {
  * "totalNum": 1,
  * "rowNum": 1
  * }
  * }
  ```

## 命名规则

参数|命名规则
:---|:---
apiName |大驼峰
api method |小写
api url | 多个单词使用 小驼峰
group |除固定 group 外，定义数据结构的 group 使用大驼峰
apiDefine name | 大驼峰

## 其他

- 如果传入参数或者返回参数会多次使用，可以使用 apiDefine apiUse 实现数据复用
- apiDescription 可以嵌入 html ，如果想加入跳转链接可以插入 `<a href="#api-group-name">group/name</a>`，group 由 apiGroup 定义，name 由 apiName 定义
