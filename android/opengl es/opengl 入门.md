# OpenGL 入门

## OpenGL 版本

- OpenGL ES 1.0 and 1.1 - This API specification is supported by Android 1.0 and higher.
- OpenGL ES 2.0 - This API specification is supported by Android 2.2 (API level 8) and higher.
- OpenGL ES 3.0 - This API specification is supported by Android 4.3 (API level 18) and higher.
- OpenGL ES 3.1 - This API specification is supported by Android 5.0 (API level 21) and higher.

## GLSurfaceView and GLSurfaceView.Renderer
GLSurfaceView 是专门用来显示 OpenGL 渲染视图的 View，通过 setRenderer() 方法设置渲染器，实现显示功能。
```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    GLSurfaceView glSurfaceView = new GLSurfaceView(this);
    glSurfaceView.setRenderer(new MyRender());
    setContentView(glSurfaceView);
}
```
GLSurfaceView.Renderer 的三个方法：  

| 返回值 | 签名 |
| -------------- | ------------------------------------------------------------------ |
|  abstract <br> void | onDrawFrame(GL10 gl) <br>Called to draw the current frame. |
|abstract <br> void|onSurfaceChanged(GL10 gl, int width, int height)<br>Called when the surface changed size.|
|abstract <br> void|onSurfaceCreated(GL10 gl, EGLConfig config)<br>Called when the surface is created or recreated.|

```java

```

## 参考链接

- [Android OpenGL ES 简明开发教程](http://wiki.jikexueyuan.com/project/opengl-es-basics/)
- [Displaying Graphics with OpenGL ES](https://developer.android.com/training/graphics/opengl/index.html)
- [OpenGL ES](https://developer.android.com/guide/topics/graphics/opengl.html)
