# Spring Tips

## RestController 文件下载

PC 端浏览器可以下载，Android 手机端浏览器不能下载。

```
 @GetMapping(value = "/down",produces = "application/vnd.android.package-archive")
```

**注意设置 ： RequestMapping 中的 produce 属性。**
