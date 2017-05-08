# spring tips

## @Autowired

### 情景：
> 1. 使用 @Component 对类进行标记
> 2. 存在同名类
> 3. 使用 @Autowired 对同名类的对象进行初始化

在上面的情景中，Spring Context 会初始化失败。

### 解决方法
1. 使用 @Component("id") 标记
2. 使用 @Qualifier("id") 配合 @Autowired 对象注入
