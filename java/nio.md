# java nio

## 概述

Java nio 由以下几个核心部分组成：

- Channels
- Buffers
- Selectors

Channel 类似与流，数据可以从 Channel 读到 Buffer，也可以从 Buffer 写到 Channel 中。

Selector 允许单线程处理多个 Channel 。如果你的应用打开了多个连接，单每个连接流量都很低，使用 Selector 就会很方便。

## FileChannel and Buffer 用法

```java
public class BufferDemo {
    private FileChannel mIn;
    private FileChannel mOut;

    public BufferDemo() throws IOException {
        Path pathA = Paths.get("/home/loucunyin/Documents/a.txt");
        mIn = FileChannel.open(pathA, StandardOpenOption.READ);
        Set<OpenOption> set = new HashSet<>();
        set.add(StandardOpenOption.WRITE);
        set.add(StandardOpenOption.READ);
        Path pathB = Paths.get("/home/loucunyin/Documents/b.txt");
        mOut = FileChannel.open(pathB,StandardOpenOption.WRITE);
    }

    public void bufferRW() throws IOException {
        ByteBuffer buffer = ByteBuffer.allocate(48);
        int bytes = mIn.read(buffer);
        while (bytes != -1) {
            buffer.flip();
            while (buffer.hasRemaining()) {
                mOut.write(buffer);
            }
            buffer.clear();
            bytes = mIn.read(buffer);
        }
    }

    public void transferFrom() throws IOException {
        mOut.transferFrom(mIn,0, mIn.size());
    }

    public void transferTo() throws IOException {
        mIn.transferTo(0, mIn.size(), mOut);
    }

    public void close() throws IOException {
        if(mIn.isOpen()){
            mIn.close();
        }
        if(mOut.isOpen()){
            mOut.close();
        }
    }
}
```

## Selector 用法

> 与Selector一起使用时，Channel必须处于非阻塞模式下。这意味着不能将FileChannel与Selector一起使用，因为FileChannel不能切换到非阻塞模式。而套接字通道都可以。

## 参考链接

- [Java NIO系列教程（一） Java NIO 概述](http://ifeve.com/overview/)
