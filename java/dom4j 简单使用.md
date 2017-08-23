# dom4j 简单使用

## 生成 Document 对象

1. 读取XML文件,获得document对象.

  ```java
  SAXReader reader = new SAXReader();
  Document  document = reader.read(new File("input.xml"));
  ```

2. 解析XML形式的文本,得到document对象.

  ```java
  String text = "<members></members>";
  Document document = DocumentHelper.parseText(text);
  ```

3. 主动创建document对象.

  ```java
  Document document = DocumentHelper.createDocument();
  Element root = document.addElement("members");// 创建根节点
  ```

TODO : 整理
