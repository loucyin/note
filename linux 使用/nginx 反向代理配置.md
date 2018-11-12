# nginx 反向代理配置

通过修改 `/etc/nginx/conf.d` ，进行修改。

## 指令

指令|使用环境|描述
:-----|:------------------------|:--------------------------------------
break  |server location if |完成当前的规则集，不在处理 rewrite 指令
if  |server location | if 指令不支持嵌套，不支持多个条件 && 和 &#124;&#124; 处理
return |server location if |结束当前规则，返回状态码
rewrite |server location if |重定向
set  |server location if |用于变量定义

## 代理指定后缀的 url

将所有 `.flv` 文件代理到 `http://172.18.18.138:8899` 。

```
location ~ \.flv$ {
    proxy_pass   http://172.18.18.138:8899;
}
```

## nginx 部署 vue 项目

vue 单页面应用，需要将路径重定向到 index.html 。

```
location / {
	try_files $uri $uri/ @router;
    index index.html;
}

location @router {
    rewrite ^.*$ /index.html last;
}
```

## 参考链接

- [nginx 常见正则匹配符号表示](https://www.cnblogs.com/netsa/p/6383094.html)
