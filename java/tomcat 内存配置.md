# tomcat 内存配置

## tomcat 崩溃

错误信息：

```
12-Oct-2016 10:12:06.642 SEVERE [http-nio-8080-exec-3] org.apache.coyote.http11.Http11Processor.service Error processing request
 java.lang.NullPointerException
    at org.apache.catalina.connector.CoyoteAdapter.service(CoyoteAdapter.java:389)
    at org.apache.coyote.http11.Http11Processor.service(Http11Processor.java:1110)
    at org.apache.coyote.AbstractProcessorLight.process(AbstractProcessorLight.java:66)
    at org.apache.coyote.AbstractProtocol$ConnectionHandler.process(AbstractProtocol.java:785)
    at org.apache.tomcat.util.net.NioEndpoint$SocketProcessor.doRun(NioEndpoint.java:1425)
    at org.apache.tomcat.util.net.SocketProcessorBase.run(SocketProcessorBase.java:49)
    at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
    at org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:61)
    at java.lang.Thread.run(Thread.java:745)
```

## 查看 jvm 内存使用情况

找到 tomcat 进程：

```
ps -eu | grep tomcat
```

查看 tomcat 的内存使用信息：

```
jmap -heap
```

```
Attaching to process ID 26752, please wait...
Debugger attached successfully.
Server compiler detected.
JVM version is 25.101-b13

using thread-local object allocation.
Parallel GC with 18 thread(s)

Heap Configuration:
   MinHeapFreeRatio         = 0
   MaxHeapFreeRatio         = 100
   MaxHeapSize              = 8373927936 (7986.0MB)
   NewSize                  = 174587904 (166.5MB)
   MaxNewSize               = 2791309312 (2662.0MB)
   OldSize                  = 349700096 (333.5MB)
   NewRatio                 = 2
   SurvivorRatio            = 8
   MetaspaceSize            = 21807104 (20.796875MB)
   CompressedClassSpaceSize = 1073741824 (1024.0MB)
   MaxMetaspaceSize         = 17592186044415 MB
   G1HeapRegionSize         = 0 (0.0MB)
Heap Usage:
Exception in thread "main" java.lang.reflect.InvocationTargetException
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at sun.tools.jmap.JMap.runTool(JMap.java:201)
        at sun.tools.jmap.JMap.main(JMap.java:130)
Caused by: java.lang.RuntimeException: unknown CollectedHeap type : class sun.jvm.hotspot.gc_interface.CollectedHeap
        at sun.jvm.hotspot.tools.HeapSummary.run(HeapSummary.java:144)
        at sun.jvm.hotspot.tools.Tool.startInternal(Tool.java:260)
        at sun.jvm.hotspot.tools.Tool.start(Tool.java:223)
        at sun.jvm.hotspot.tools.Tool.execute(Tool.java:118)
        at sun.jvm.hotspot.tools.HeapSummary.main(HeapSummary.java:49)
        ... 6 more
```

##

## 参考链接

- [Linux 下修改Tomcat使用的JVM内存大小](http://www.cnblogs.com/sixiweb/archive/2012/11/25/2787591.html)
- [查看jvm内存使用情况的命令jmap](http://blog.sina.com.cn/s/blog_9fd5b6df01010xmj.html)
- [PermGen space Error in tomcat](http://stackoverflow.com/questions/10392255/permgen-space-error-in-tomcat)
- [Xms Xmx PermSize MaxPermSize 区别](http://www.cnblogs.com/mingforyou/archive/2012/03/03/2378143.html)
- [Jmap命令查看内存使用情况](http://blog.csdn.net/wwd0501/article/details/50216367)
- [JVM性能调优监控工具jps、jstack、jmap、jhat、jstat使用详解](http://www.open-open.com/lib/view/open1390916852007.html)
- [java高分局之jstat命令使用](http://blog.csdn.net/maosijunzi/article/details/46049117)
- [java 虚拟机--新生代与老年代GC](https://my.oschina.net/sunnywu/blog/332870)
