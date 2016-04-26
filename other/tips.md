## Sequence Or UUID
### Sequence 自增序列，使用简单，有顺序，但是会暴露一些信息。  
比如说，不同用户的图片挂到 tomcat 上，为了防止图片重名，图片的命名以 Sequence 的方式进行命名，一般情况下，是通过服务器获取图片的 URL，但是，用户通过更改 Sequence 的方式，就有可能获取到不属于他的图片。
### UUID （Universally Unique Identifier）通用唯一标识码。
UUID 由 32 位十六进制数加 - 组成，格式为 8-4-4-16 或者 8-4-4-4-12 。UUID 的缺陷是生成的结果串会比较长。  
同样是上面的问题，通过 UUID 的方式为图片命名，同样能防止图片重名，并且其他用户不能轻易的拿到不属于他的图片。
## [MD5](http://baike.baidu.com/link?url=sSvCIsj-Ybg5u9SNW6luqjrKIBFPCJF1V9jlDCGO3cHthOmaYr5A7XidMEI44u63Bm_zNRAssPu61o1e6rnSWK) 的使用
> Message Digest Algorithm MD5（中文名为消息摘要算法第五版）为计算机安全领域广泛使用的一种散列函数，用以提供消息的完整性保护。  

- MD5 可以用来加密用户的密码信息，通过保存用户密码的 MD5 ，而不是明码，提高安全性。
- 但是仅仅是通过简单的计算 MD5 并不安全，现在有很多 MD5 词典，可以通过查阅词典，获取 MD5 对应的简单数据值。
- 可以通过在 MD5 上另作简单计算，提高破解难度。
- 使用其他的加密方式。  
## 列表视图的使用
请求数据的操作一定不能放到 Item 中来完成。
