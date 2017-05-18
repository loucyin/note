# RestEasy 文件上传下载

## 配置依赖
文件需要使用表单上传，需要如下依赖：
```groovy
compile group: 'org.jboss.resteasy', name: 'resteasy-multipart-provider', version: '3.1.2.Final'
```

## 上传代码
### 通过 MultipartFormDataInput 解析表单数据。

```java
@Override
@POST
@Path(ALERTS_UPLOAD)
@Consumes({MediaType.MULTIPART_FORM_DATA})
public ResponseData upload(@MultipartForm MultipartFormDataInput input) {
    try {
      Map<String,List<InputPart>> map = input.getFormDataMap();
      List<InputPart> parts = map.get(AlertConst.FORM_IMAGE_FILE);
      List<InputPart> types = map.get(AlertConst.FORM_IMAGE_TYPE);
      byte type = 0;
      if(types != null){
        InputPart part = types.get(0);
        try {
            type = Byte.parseByte(part.getBodyAsString());
        } catch (NumberFormatException | IOException e) {
            e.printStackTrace();
        }
      }
      int count = 0;
      for (InputPart imagePart:parts) {
        InputStream inputStream = imagePart.getBody(InputStream.class, null);
        String name = UUID.randomUUID().toString()+"."+imagePart.getMediaType().getSubtype();
        File file = ResourcePathUtils.createNewFile(name);
        ResourceWriter writer = new ResourceWriter(inputStream,file);
        writer.write();
        BaseAlertResource resource = new BaseAlertResource();
        resource.setPath(file.getAbsolutePath());
        resource.setResourceType(type);
        resource.setAlertId(alertId);
        resource.setContentType(imagePart.getMediaType().getType());
        resource.setResourceIndex((byte) count);
        resourceBaseMapper.insertSelective(resource);
        count++;
      }
    } catch (IOException e) {
        e.printStackTrace();
        return ResponseData.error(AlertError.SAVE_FILE_ERROR);
    }
    return ResponseData.ok();
}
```

### 通过自定义对象接收数据

```java
public class ImageForm {
    @FormParam("imageData")
    @PartType(MediaType.APPLICATION_OCTET_STREAM)
    private byte[] imageData;

    @FormParam("name")
    private String name;

    public byte[] getImageData() {
        return imageData;
    }


    public void setImageData(byte[] imageData) {
        this.imageData = imageData;
    }

    public String getName() {
        return name;
    }


    public void setName(String name) {
        this.name = name;
    }

    public void writeToFile(String path) throws IOException {
        System.out.println(path);
        File file = new File(path);
        if(!file.exists()){
            file.createNewFile();
        }

        FileOutputStream outputStream = new FileOutputStream(file);
        outputStream.write(imageData);
        outputStream.close();
    }
}
```

```java
@POST
@Path("upload")
@Consumes({ "multipart/form-data; charset=UTF-8", "text/xml; charset=UTF-8" })
@Produces({ "application/json" })
public ResponseData upload(@MultipartForm  ImageForm form){
    System.out.println("upload");
    try {
        form.writeToFile("D:\\1.jpg");
    } catch (IOException e) {
        e.printStackTrace();
    }
    return new ResponseData();
}
```

MultipartFormDataInput 可以获取到更多的上传信息。

## 文件下载

### 通过 javax.ws.rs.core.Response.ResponseBuilder 构建响应消息

```java
@Override
@GET
@Path(ALERTS_RESOURCE)
public Response getResource(@PathParam(ID) Long alertId, @QueryParam(TYPE) Byte type, @QueryParam(INDEX) Byte index, @HeaderParam(USER_ID) Long userId) {
    BaseAlertResource resource = alertService.getResource(alertId, type, index);
    if (resource == null) {
        return Response.status(Response.Status.NOT_FOUND).build();
    }
    File file = new File(resource.getPath());
    Response.ResponseBuilder builder = Response.ok(file,resource.getContentType());

    return builder.build();
}
```
