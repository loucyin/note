# Spring MVC 文件上传下载

## form 表单文件上传

### 单文件上传

spring mvc 通过 MultipartFile 接收上传文件，通过 `transferTo(file:File)` 方法保存上传文件。

```kotlin
@RequestMapping(value = "upload",method = arrayOf(RequestMethod.POST))
fun upload(@RequestParam("description") description:String,@RequestParam("file") multipartFile:MultipartFile):String{
    println(description)
    val dir = File("build")
    val file = File(dir.absolutePath + File.separator + multipartFile.originalFilename)
    multipartFile.transferTo(file)
    println(file.absolutePath)
    return "success"
}
```

form 表单，注意要设置 `enctype="multipart/form-data"` ，否则 Spring MVC 不能解析：

```html
<form action="http://localhost:8184/test/upload" method="post" enctype="multipart/form-data">
  <input type="text" name="description" value="description">
  <input type="file" name="file" value="" multiple="multiple">
  <input type="submit">
</form>
```

**上传多个文件时，只能接收到第一个文件。**

### 多文件上传

将 MultipartFile 改为 List<MultipartFile> 即可接收多文件.

```kotlin
@RequestMapping(value = "multi-upload",method = arrayOf(RequestMethod.POST))
fun uploadFiles(@RequestParam("description") description:String,@RequestParam("file") files:List<MultipartFile>):String{
    val dir = File("build")
    files.forEach {
        val file = File(dir.absolutePath + File.separator + it.originalFilename)
        it.transferTo(file)
        println(file.absolutePath)
    }
    return "success"
}
```

### 自定义数据类型

```kotlin
class UploadBean :Serializable{
    lateinit var description:String
    lateinit var file:MultipartFile
}
```

```kotlin
@RequestMapping(value = "upload-bean",method = arrayOf(RequestMethod.POST))
fun upload( uploadBean: UploadBean):String{
    println(uploadBean)
    val dir = File("build")
    val file = File(dir.absolutePath + File.separator + uploadBean.file.originalFilename)
    uploadBean.file.transferTo(file)
    println(file.absolutePath)
    return "success"
}
```

## 文件下载

文件下载一般有两种方式，第一种是直接通过 HttpServletResponse 的 outPutStream 输出；第二种是返回 ResponseEntity 对象。

### ResponseEntity 使用

```kotlin
@RequestMapping("download")
fun  download():ResponseEntity<ByteArray>{
    val dir =File("build")
    val file = dir.listFiles().filter { !it.isDirectory }.first()
    val header = HttpHeaders()
    header.setContentDispositionFormData("attachment",file.name)
    val response = ResponseEntity(FileUtils.readFileToByteArray(file),header,HttpStatus.OK)

    return response
}
```
