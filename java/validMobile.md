## java 判断有效手机号
```java
public boolean isValidMobile(String mobile){
  Pattern pattern = Pattern.compile("^((1[3,5,8][0-9])|(14[5,7])|(17[0,1,6,7,8]))\\d{8}$");
  Matcher matcher = pattern.matcher(mobile);
  return matcher.matches();
}
```
## 参考链接
- [判断是否手机号码--java](http://blog.csdn.net/lonewolf521125/article/details/45483855)
- [国内手机号码匹配（2016年）](http://www.oschina.net/code/snippet_149862_45861)
