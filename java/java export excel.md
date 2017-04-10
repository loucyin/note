# Java export Excel

## 依赖
```groovy
compile group: 'org.apache.poi', name: 'poi', version: '3.15'
```

## 导出 excel
步骤：
- 创建 workbook
- 使用 workbook 创建 row
- 使用 row 创建 cell
- 写入内容到 cell

```java
private static void createWorkBook() throws IOException {
    Workbook workbook = new HSSFWorkbook();
    Sheet sheet = workbook.createSheet();
    Row titleRow = sheet.createRow(0);
    String[] titles = {"ID","姓名","年龄"};
    for (int i = 0; i < titles.length; i++) {
        Cell cell = titleRow.createCell(i);
        cell.setCellValue(titles[i]);
    }

    List<User> users = createUsers();
    for (int i = 0; i < users.size(); i++) {
        User user = users.get(i);
        Row row = sheet.createRow(i+1);
        Cell id = row.createCell(0);
        id.setCellValue(user.getId());
        Cell name = row.createCell(1);
        name.setCellValue(user.getName());
        Cell age = row.createCell(2);
        age.setCellValue(user.getAge());
    }

    File file = new File(PATH);
    FileOutputStream fos = new FileOutputStream(file);
    workbook.write(fos);
    workbook.close();
    fos.close();
}
```

设置日期显示格式：
```java
Cell date = row.createCell(3);
date.setCellValue(user.getDate());
CellStyle style = workbook.createCellStyle();
style.setDataFormat(workbook.createDataFormat().getFormat("yyyy-MM-dd HH:mm:ss"));
date.setCellStyle(style);
```

## 读取 excel
步骤：
- 读取 Workbook
- 读取 Row
- 读取 Cell

```java
public static void readWorkBook(String path) throws IOException, InvalidFormatException {
    File file = new File(path);
    Workbook workbook = WorkbookFactory.create(file);
    workbook.close();
    Sheet sheet = workbook.getSheetAt(0);
    for (Row row:sheet){
        int num = row.getRowNum();
        if(num == 0){
            for (Cell cell:row){
                System.out.println("title:"+cell.getStringCellValue());
            }
        }else {
            User user = new User();
            Cell id = row.getCell(0);
            Cell name = row.getCell(1);
            Cell age = row.getCell(2);
            user.setId((int) id.getNumericCellValue());
            user.setName(name.getStringCellValue());
            user.setAge((int) age.getNumericCellValue());
            System.out.println(user);
        }
    }
}
```

## 参考链接
- [Java 实现导出excel表 POI](http://www.cnblogs.com/bmbm/archive/2011/12/08/2342261.html)
