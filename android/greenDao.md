## 关于 id 的 autoincrement
```java
@Entity
public class AdImageInfo{
    @Id(autoincrement = true)
    private Long id;
    private String path;
    private String detailUrl;
    private String image;
}
```
设置 autoincrement = true 时，id 必须 Long 类型，而不能使用 long 基本数据类型。
