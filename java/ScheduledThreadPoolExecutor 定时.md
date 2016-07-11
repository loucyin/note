## 代码
```java
private static ScheduledThreadPoolExecutor EXECUTOR = new ScheduledThreadPoolExecutor(2);
Runnable task = new MyTask(phone);
EXECUTOR.schedule(task, RESEND_TIME, TimeUnit.SECONDS);
// 用于清除短信发送记录
private static class MyTask implements Runnable {
	String mPhone;

	public MyTask(String phone) {
		mPhone = phone;
	}

	@Override
	public void run() {
		for (String string : PHONE_LIST) {
			if (string.equals(mPhone)) {
				PHONE_LIST.remove(string);
				return;
			}
		}
	}
}
```
## 参考链接
- [利用ScheduledThreadPoolExecutor定时执行任务](http://blog.csdn.net/kazeik/article/details/8545049)
- [关于Timer的几个问题](http://blog.csdn.net/winniepu/article/details/4601228)
