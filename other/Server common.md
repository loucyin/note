# Server Common

## 登录逻辑

### 需要的参数

- loginKey : 经过 base64 加密处理过的
- loginName ： 登录名
- loginPwd : 经过 base64 加密处理过的

### 用到的表格

- user : 记录用户基本信息
- login : 记录登录信息
- session : 防止用户在相同类型的客户端多次登录

### 逻辑处理

#### 密码的处理

- 与密码相关的字段<br>
  password randomString

- 数据库中的存储密码<br>
  数据库中存放的密码为： MD5(MD5(明文密码)+randomString)

- 用户登录的时候接口中的密码<br>
  Base64(MD5(明文密码))

#### 登录后的逻辑

- 记录登录信息
- 记录 Session 信息<br>
  用于防止同一用户的多次登录

#### 对于需要验证用户的功能

- 通过 tokenId 获取 userId。
