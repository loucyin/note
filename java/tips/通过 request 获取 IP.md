# 通过 HttpServletRequest 获取 IP

```java
public String getIpAddr(HttpServletRequest request) {  
   String ip = request.getHeader("x-forwarded-for");  
   if(ip == null || ip.length() == 0 || "unknown".equalsIgnoreCase(ip)) {  
       ip = request.getHeader("Proxy-Client-IP");  
   }  
   if(ip == null || ip.length() == 0 || "unknown".equalsIgnoreCase(ip)) {  
       ip = request.getHeader("WL-Proxy-Client-IP");  
   }  
   if(ip == null || ip.length() == 0 || "unknown".equalsIgnoreCase(ip)) {  
       ip = request.getRemoteAddr();  
   }  
   return ip;  
}
```

## 参考链接

- [Java获取客户端真实IP地址的两种方法](http://dpn525.iteye.com/blog/1132318)
