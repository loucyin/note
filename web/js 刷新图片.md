# js 刷新验证码

## 两种方式

```javascript
// 原生JS
document.getElementById("imageId").src = "xxxx.jpg";
// jquery
$("#imageId").attr("src","xxxx.jpg");
```

## 相同的链接不刷新

如果链接不改变，会从缓存中加载图片。

**解决办法** 在链接后加一个随机数参数。

## 参考链接

- [网页中通过js修改img的src属性刷新图片时，图片缓存问题现象表述及问题解决](http://blog.csdn.net/goodleiwei/article/details/50737548)
