## 一段代码
```java
public class Thread2 implements Runnable
{
	@Override
	public void run()
	{
		System.out.println("run.");
		throw new RuntimeException("Exception");
	}

	public static void main(String[] args)
	{
		Thread thread2 = new Thread(new Thread2());
		thread2.start();
		System.out.println("End Method.");
	}
}
```
## 意外的输出结果
```
End Method.
run.
Exception in thread "Thread-0" java.lang.RuntimeException: Exception
	at demo.Thread2run.(Thread2.java:9)
	at java.lang.Thread.run(Thread.java:745)
```
```
Exception in thread "Thread-0" java.lang.RuntimeException: Exception
	at demo.Thread2.run(Thread2.java:9)
	at java.lang.Thread.run(Thread.java:745)
End Method.
run.
```
## 疑问
**run. 怎么会在 Exception 之后显示**
## java 未捕获异常的处理
java 文档中找到的关于未捕获异常处理的说明。
> If no `catch` clause that can handle an exception can be found, then the current thread (the thread that encountered the exception) is terminated. Before termination, all finally clauses are executed and the uncaught exception is handled according to the following rules:  
- If the current thread has an uncaught exception handler set, then that handler is executed.
- Otherwise, the method uncaughtException is invoked for the ThreadGroup that is the parent of the current thread. If the ThreadGroup and its parent ThreadGroups do not override uncaughtException, then the default handler's uncaughtException method is invoked.

如果没有 catch 块处理异常，当前线程（遇到异常的线程）会被终止。在终止之前，所有的 finally 块被执行，并且未捕获的异常会根据下面的异常处理：  
- 如果当前线程有设置了未捕获异常 handler ，handler 会被执行。
- 否则，当前线程的 parent ThreadGroup 的 uncaughtException 方法会被执行，如果 ThreadGroup 和它的 parent ThreadGroups 没有重写 uncaughtException ，那么默认 handler 的 uncaughtException 方法会被执行。  
ThreadGroup 实现了 Thread.UncaughtExceptionHandler 接口，如下。
```java
public void uncaughtException(Thread t, Throwable e) {
    if (parent != null) {
        parent.uncaughtException(t, e);
    } else {
        Thread.UncaughtExceptionHandler ueh =
            Thread.getDefaultUncaughtExceptionHandler();
        if (ueh != null) {
            ueh.uncaughtException(t, e);
        } else if (!(e instanceof ThreadDeath)) {
            System.err.print("Exception in thread \""
                             + t.getName() + "\" ");
            e.printStackTrace(System.err);
        }
    }
}
```
在没有 parent 和 defaultUncaughtExceptionHandler 的情况下，就会调用 System.err 输出 Exception 。

## System.out and System.err
out err 都是 System 的静态成员变量，并且都是 PrintStream 类型。在 println 中有一个同步锁。
```java
public void println(String x) {
		synchronized (this) {
				print(x);
				newLine();
		}
}
```
println(String) 最终是调用 BufferedWriter 进行输出的，而 BufferedWriter 是对 OutputStreamWriter 的装饰。  

## 对于上面的疑问
信息写入到 out err 对象肯定是按顺序进行的。
```
End Method. >> out
run. >> out
Exception in thread "Thread-0" java.lang.RuntimeException: Exception
	at demo.Thread2.run(Thread2.java:9)
	at java.lang.Thread.run(Thread.java:745) >> err
```
但从 out err 输出到 console ，只有一个 console 对象，不能保证 out err 谁先占用，所以会出现上面的情况。
## 验证代码
给 Thread 设置一个 DefaultUncaughtExceptionHandler ，利用 System.out 输出 Exception 信息。那么得到的顺序肯定是唯一的。
```java
public class Thread2 implements Runnable {
	@Override
	public void run() {
		System.out.println("run.");
		throw new RuntimeException("Exception");
	}

	public static void main(String[] args) {
		Thread.setDefaultUncaughtExceptionHandler(new Handler());
		Thread thread2 = new Thread(new Thread2());
		thread2.start();
		System.out.println("End Method.");
	}

	static class Handler implements Thread.UncaughtExceptionHandler {

		@Override
		public void uncaughtException(Thread t, Throwable e) {
			System.out.print("Exception in thread \"" + t.getName() + "\" ");
			e.printStackTrace(System.out);
		}

	}
}
```

## 关于异常的其他事项
### java Throwable 层次结构
![java Throwable 层次结构](image/Throwable.jpg)
### finally 块中出现 return  
[这有一段神奇的代码](http://blog.csdn.net/hguisu/article/details/6155636)  
finally 块中出现 return ，编译器会提示：finally block does not complete normally ;之前，try 中的返回值，会被覆盖，catch 抛出的异常，也会被吞掉。
### 异常处理误区
[Java 异常处理的误区和经验总结](http://www.ibm.com/developerworks/cn/java/j-lo-exception-misdirection/)  
待整理
## 参考链接  
- [java RuntimeException](http://lelglin.iteye.com/blog/1454999)
- [深入理解java异常处理机制](http://blog.csdn.net/hguisu/article/details/6155636)
- [Java 异常处理的误区和经验总结](http://www.ibm.com/developerworks/cn/java/j-lo-exception-misdirection/)
- [Java中的异常处理详解](http://blog.csdn.net/javaeeteacher/article/details/6309246)  
- [驯服 Tiger: 线程中的默认异常处理](http://www.ibm.com/developerworks/cn/java/j-tiger08104/)
- [Who calls the main function in java?](http://stackoverflow.com/questions/3949642/who-calls-the-main-function-in-java)
- [11.3. Run-Time Handling of an Exception](https://docs.oracle.com/javase/specs/jls/se8/html/jls-11.html#jls-11.3)
- [14.20.2. Execution of try-finally and try-catch-finally](https://docs.oracle.com/javase/specs/jls/se8/html/jls-14.html#jls-14.20.2)
- [利用 Java SE 7 异常处理方面的改进](http://www.oracle.com/technetwork/cn/articles/java/java7exceptions-1404186-zhs.html)
- [finally块的问题(finally block does not complete normally)](http://blog.csdn.net/chh_jiang/article/details/4557461)
