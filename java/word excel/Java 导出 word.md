# Java 导出 word

## 步骤
- 用word写一个需要导出的word模板，然后存为xml格式。
- 将xml中需要动态修改内容的地方，换成freemarker的标识符。

## 依赖
```groovy
compile group: 'org.freemarker', name: 'freemarker', version: '2.3.23'
```

## 导出 word
```java
public static void export(String basePackagePath, String templateName, Map<String,Object> map, String outName) throws IOException, TemplateException {
    Configuration configuration = new Configuration(Configuration.VERSION_2_3_23);
    configuration.setDefaultEncoding("utf-8");
    configuration.setClassLoaderForTemplateLoading(WordDocUtils.class.getClassLoader(),basePackagePath);

    Template template =configuration.getTemplate(templateName);
    File file = new File(outName);
    if(!file.exists() && !file.createNewFile()){
        return;
    }

    Writer writer = new OutputStreamWriter(new FileOutputStream(file));
    template.process(map,writer);
    writer.close();
}
```

## 导出图片
- word xml 中图片是以 base64 编码存储的
- 需要先放置一个图片占位符
- 将图片的内容替换成 freemarker 占位符
- 多张图片导入，注意修改 imagedata src 与 binData w:name 相对应
- 通过修改 shape style 修改图片大小

```xml
<w:pict>
    <v:shapetype id="_x0000_t75" coordsize="21600,21600" o:spt="75" o:preferrelative="t" path="m@4@5l@4@11@9@11@9@5xe" filled="f" stroked="f">
      <v:stroke joinstyle="miter"/>
        <v:formulas>
          <v:f eqn="if lineDrawn pixelLineWidth 0"/>
          <v:f eqn="sum @0 1 0"/><v:f eqn="sum 0 0 @1"/>
          <v:f eqn="prod @2 1 2"/><v:f eqn="prod @3 21600 pixelWidth"/><v:f eqn="prod @3 21600 pixelHeight"/>
          <v:f eqn="sum @0 0 1"/><v:f eqn="prod @6 1 2"/>
          <v:f eqn="prod @7 21600 pixelWidth"/>
          <v:f eqn="sum @8 21600 0"/>
          <v:f eqn="prod @7 21600 pixelHeight"/>
          <v:f eqn="sum @10 21600 0"/>
        </v:formulas>
        <v:path o:extrusionok="f" gradientshapeok="t" o:connecttype="rect"/>
        <o:lock v:ext="edit" aspectratio="t"/>
    </v:shapetype>
    <w:binData w:name="wordml://0200000${log.id}.jpg" xml:space="preserve">${log.policeImageContent}</w:binData>
    <v:shape id="_x0000_i1025" type="#_x0000_t75" style="width:243.75pt;height:138pt">
      <v:imagedata src="wordml://0200000${log.id}.jpg" o:title="Penguins"/>
    </v:shape>
</w:pict>
```

## 参考链接
- [用 Freemarker 生成 word 文档](http://www.cnblogs.com/zhuyoufeng/archive/2011/09/01/2161558.html)
