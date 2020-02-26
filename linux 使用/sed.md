# sed 使用

## b t lable

- b label ,无条件跳转到标签label,如果label没有指定,跳转到命令的结尾;
- t label ,如果最后一次输入的最后一个 s/// 子命令执行成功,跳转到标签label,如果label没有指定,跳转到命令的结尾;

```bash
sed -i ":a;N;s/;[\n\s]*INSERT INTO \`.*\` VALUES/,\n/g;ba" test.sql
```

## 参考链接

- [sed标签b t](http://www.bubuko.com/infodetail-1655818.html)
