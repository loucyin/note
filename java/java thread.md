# java thread
![线程的生命周期](./image/threadstates.jpg)  
[图片来源](http://www.javatpoint.com/life-cycle-of-a-thread)

## wait() and notify()

wait() 、notify() 、notifyAll()，只能出现在 synchronized 块或者 synchronized 方法中。

- 执行 wait() 后，线程进入阻塞状态
- notify()、notifyAll() 可以唤醒通过 wait() 进入阻塞状态下的线程
- 一般情况下 wait() 、notify() 是成对出现的

## join()

### void join()   

当前线程等该加入该线程后面，等待该线程终止。    

### void join(long millis)    

当前线程等待该线程终止的时间最长为 millis 毫秒。 如果在millis时间内，该线程没有执行完，那么当前线程进入就绪状态，重新等待cpu调度   

### void join(long millis,int nanos)    

等待该线程终止的时间最长为 millis 毫秒 + nanos 纳秒。如果在millis时间内，该线程没有执行完，那么当前线程进入就绪状态，重新等待cpu调度

## 终止线程

1. 正常执行完run方法，然后结束掉
2. 控制循环条件和判断条件的标识符来结束掉线程

## Lock and Condition
Lock 比synchronized更灵活、更具可伸缩性的锁定机制;ReentrantLock、ReentrantReadWriteLock都是对象层面的锁定，要保证锁定一定会被释放，就必须将unLock()放到finally{}中；

StampedLock 在 java8 java.util.concurrent.locks 中新增的一个API。控制锁有三种模式（写，读，乐观读），一个StampedLock状态是由版本和模式两个部分组成，锁获取方法返回一个数字作为票据stamp，它用相应的锁状态表示并控制访问，数字0表示没有写锁被授权访问。在读锁上分为悲观锁和乐观锁。

ReentrantLock simple:

```java
public class LockTest {
    public static void main(String[] args) {
        ReentrantLock lock = new ReentrantLock();
        Condition condition = lock.newCondition();

        Thread t1 = new Thread(new Runnable() {
            @Override
            public void run() {
                lock.lock();
                try {
                    System.out.printf("%s start%n",Thread.currentThread().getName());
                    System.out.printf("%s wait signal%n",Thread.currentThread().getName());
                    condition.await();
                    System.out.printf("%s over%n",Thread.currentThread().getName());
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } finally {
                    lock.unlock();
                }
            }
        });

        Thread t2 = new Thread(new Runnable() {
            @Override
            public void run() {
                lock.lock();
                try {
                    System.out.printf("%s start%n",Thread.currentThread().getName());
                    condition.signal();
                    System.out.printf("%s send signal%n",Thread.currentThread().getName());
                    System.out.printf("%s over%n",Thread.currentThread().getName());
                }finally {
                    lock.unlock();
                }
            }
        });

        t1.start();
        t2.start();

    }
}
```
Condition 的 await() , signal() 方法与 wait() , notify() 方法相对应。

## Semaphore

Semaphore可以控制某个资源可被同时访问的个数，通过 acquire() 获取一个许可，如果没有就等待，而 release() 释放一个许可，acquire() 获取可以是随机的也可以是按顺序获取的。

一个经典的问题：

Thread 1 : 只负责打印 a;  
Thread 2 : 只负责打印 b;  
Thread 3 : 只负责打印 c;  
Thread 4 : 只负责打印 d;  

利用4个线程输出 a,b,c,d 到 4 个文件中：  

file1 : abcdabcd  
file2 : bcdabcda  
file3 : cdabcdab  
file4 : dabcdabc  

```java
public class FileHelper {
    Semaphore mSemaphore = new Semaphore(1);
    StringBuilder mBuilder = new StringBuilder();
    int mIndex;

    public FileHelper(int index) {
        mIndex = index;
    }

    public void append(char c){
        mBuilder.append(c);
        System.out.printf("%s write %c to file%d%n",Thread.currentThread().getName(),c,mIndex);
    }
}
```

```java
public class Writer implements Runnable{
    FileHelper[] mHelpers;
    int mIndex;
    boolean isReady;
    int mCount ;

    public Writer(FileHelper[] helpers, int index,int count) {
        mHelpers = helpers;
        mIndex = index;
        mCount = count;
    }

    public void setIsReady(boolean ready) {
        isReady = ready;
    }

    @Override
    public void run() {
        try {
            mHelpers[(mIndex)%mHelpers.length].mSemaphore.acquire();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        while (true) {
            if (isReady) {
                for (int i = 0; i > -mCount; i--) {
                    int index = (mIndex + i) % mHelpers.length;
                    int next = (mIndex + i + 1) % mHelpers.length;
                    if(index < 0){
                        index += mHelpers.length;
                    }
                    if(next <0){
                        next += mHelpers.length;
                    }
                    FileHelper helper = mHelpers[index];
                    FileHelper nextHelper = mHelpers[next];
                    try {
                        if (i != 0) {
                            helper.mSemaphore.acquire();
                        }
                        helper.append((char) ('a' + mIndex));

                        nextHelper.mSemaphore.release();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                break;
            }
        }
    }

    public static void main(String[] args) throws InterruptedException {
        int number = 3 ;
        ExecutorService service = Executors.newFixedThreadPool(number);
        FileHelper[] helpers = new FileHelper[number];
        for (int i = 0; i < number; i++) {
            helpers[i] = new FileHelper(i);
            if(i!=0){
                helpers[i].mSemaphore.acquire();
            }
        }

        Writer[] writers = new Writer[number];
        Future[] futures = new Future[number];
        for (int i = 0; i < number; i++) {
            writers[i] = new Writer(helpers, i,20);
            futures[i] = service.submit(writers[i]);
        }

        for (int i = 0; i < number; i++) {
            writers[i].setIsReady(true);
        }

        for (int i = 0; i < number; i++) {
            try {
                futures[i].get();
            } catch (ExecutionException e) {
                e.printStackTrace();
            }
            FileHelper helper = helpers[i];
            System.out.printf("file%d : %s%n",helper.mIndex,helper.mBuilder.toString());
        }
        service.shutdownNow();
    }
}
```
