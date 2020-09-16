# ffmpeg 一次参数调优经历

## 背景

基于摄像头实况流的播放系统

原始的系统：

- ffmpeg 从摄像头拉取实况流，推送到流媒体服务器
- 流媒体服务器复制分发出去

需求：

- 客户端播放同一路摄像头的实况，但是要展示不同的效果

这里，需要展示的不同效果通过实现 ffmpeg 的 filter 实现。

改进：

- ffmpeg 从摄像头拉取实况流 (rtsp)，推送到流媒体服务器(rtmp)
- 流媒体服务器将实况流复制分发出去(http-flv & rtmp)
- 需要特殊展示的实况流，由 ffmpeg 拉取从流媒体服务器分发的流(rtmp)，处理后，再推送到流媒体服务器(rtmp)
- 流媒体服务器将特殊展示的流复制分发出去(http-flv & rtmp)

由于相机能对外提供的 rtsp 流路数有限制，这里没有考虑同时从相机拉取多路流，而是拉取流媒体复制分发后的流。

## 遇到的问题

**可以预知的问题**：这里经过两次 ffmpeg 的类型转换，会有一定的延时。

**延时有点高**：方案实现之后，打开 VLC 播放，二次分发的流，发现加载延时有 20 s 左右，这个延时已经超出了能够承受的范围。这里的客户端主要是面向浏览器，使用 flv.js 测试了一下，发现加载延时 6s 左右，这个延时还算能够接受。

**错误的关注点**：**加载延时**，和小伙伴讨论问题的时候发现的，虽然加载延时会影响客户的体验，但是这不是关键点，摄像头实况，关注点应该在**实况延时**上面，有点凑巧，加载延时和实况延时差不多。

**小伙伴提出的问题**：在局域网内进行测试，不存在传输问题，6s 的延时有点高，这个延时出在哪里了？

**猜测**：视频流 gop 导致的，ffmpeg 在解析流的时候，需要检测到第一个关键帧，才能开始处理，相机的 gop 默认设置是 50 也就是 2s 有一个关键帧，经过两次 ffmpeg 转换，最高会有 4 s 的延时。

## 验证

思路很简单：固定参数进行对比。

之前使用 ffmpeg 时，已知添加参数： `-preset ultrafast`，指定多线程 `-threads 4` ，可以加快 ffmpeg 的处理速度。查看 ffmpeg 文档，发现可以通过 `-g` 指定输出视频的 gop，因为这里需要做特殊处理，对视频做了编解码，涉及到 `-c:v libx264`。

要对比的 ffmpeg 参数有这几个：

- `-c:v libx264`
- `-preset ultrafast`
- `-threads `
- `-g`

另外，就是经过 1 次转换和 2 次转换的对比。

### 一次转换对比

先验证单次 ffmpeg 转换的延时，基准命令为：

```bash
ffmpeg -i rtsp://admin:abcd1234@181.181.1.253:554/Streaming/Channels/101?transportmode=unicast&profile=Profile_1 -an -c:v copy -f flv rtmp://localhost/copy/1
```

基准参考：`-c:v copy`从客户端播放的流对比实时有 1s 左右的延时。

下面是修改参数后，与基准参考的对比数据：

对比参数                                        | 时间差（s）
:-----------------------------------------------|:------
-c:v libx264                                    | 3
-c:v libx264 -g 50                              | 3
-c:v libx264 -g 50 -preset ultrafast            | 2
-c:v libx264 -g 50 -preset ultrafast -threads 4 | 1
-c:v libx264 -threads 4                         | 3
-c:v libx264 -preset ultrafast                  | 2
-c:v libx264 -preset ultrafast -threads 4       | 1
-c:v libx264 -g 50 -preset ultrafast -threads 2 | 1
-c:v libx264 -g 50 -preset ultrafast -threads 3 | 1
-c:v libx264 -g 50 -preset ultrafast -threads 5 | 1

结论：

- `-preset ultrafast ` 可以减少实况延迟
- 单独配置 `-threads 4` 不会减少延迟，和 `-preset ultrafast ` 配合使用会有效果
- 两条线程数即可减小实况延时，再增加线程数对减小实况延时作用不明显
- `-g 50` 不会减少实况延迟，但是会缩短客户端平均加载视频的时间，同时会增加视频的码率

### 二次转换对比

数据流：

相机 -> ffmpeg -> nms -> ffmpeg -> nms -> client

ffmpeg  拉取相机的 rtsp 流，转成 rtmp 流，推到 nms ，nms 分发 rtmp 流，第二路 ffmpeg 拉取 rtmp 流，推送到 nms , nms 将 http-flv 下发给客户端。

基准参考：对比第一路 ffmpeg 的 rtmp 流。

时间差：第二路 ffmpeg 进程首次启动时间差/非首次启动时间差。

#### 测试 1

第一路 ffmpeg 命令 1：

```bash
ffmpeg -i rtsp://admin:abcd1234@181.181.1.253:554/Streaming/Channels/101?transportmode=unicast&profile=Profile_1 -an -c:v copy -f flv rtmp://localhost/copy/1
```

第二路 ffmpeg 参数 | 时间差(s)
:-----------------------------------------------|:----------
-c:v copy | 4/-1
-c:v libx264 | 5/4
-c:v libx264 -preset ultrafast |5/4
-c:v libx264 -preset ultrafast -threads 2 |5/1
-c:v libx264 -preset ultrafast -threads 3 | 3/0

#### 测试 2

第一路 ffmpeg 命令 2：

```bash
ffmpeg -i rtsp://admin:abcd1234@181.181.1.253:554/Streaming/Channels/101?transportmode=unicast&profile=Profile_1 -an -c:v libx264 -preset ultrafast -threads 2 -f flv -g 25 rtmp://localhost/copy/1
```

第二路 ffmpeg 参数 | 时间差(s)
:-----------------------------------------------|:---------
-c:v copy | 5/-1
-c:v libx264 | 5/5
-c:v libx264 -preset ultrafast |4/2
-c:v libx264 -preset ultrafast -threads 2 |5/1
-c:v libx264 -preset ultrafast -threads 3 | 4/0

#### 测试 3

第一路 ffmpeg 命令 1：

```bash
ffmpeg -i rtsp://admin:abcd1234@181.181.1.253:554/Streaming/Channels/101?transportmode=unicast&profile=Profile_1 -an -c:v copy -f flv rtmp://localhost/copy/1
```

第二路 ffmpeg 参数 | 时间差(s)
:-----------------------------------------------|:----------
-c:v copy -g 25 |5/0
-c:v libx264 -g 25 |5/4
-c:v libx264 -preset ultrafast -g 25 |3/2
-c:v libx264 -preset ultrafast -threads 2 -g 25 |4/0
-c:v libx264 -preset ultrafast -threads 3 -g 25 | 4/1

结论:

- 第二路 ffmpeg 首次启动延时与实况相比有 4-6 秒左右的延时
- 第二路 ffmpeg 使用 `-c:v copy` 或者 `-c:v libx264 -preset ultrafast -threads 2` 可以缩短再次播放时的延时，与实况相比有 2s 左右的延时
- 第一路 ffmpeg 中加入 `-c:v libx264 -preset ultrafast -threads 2 -g 25` 参数和使用 `-c:v copy` 区别不大
- 第二路 ffmpeg 命令中加入 `-g 25` 可以明显缩短客户端播放平均加载时间
- 首次 ffmpeg 进程启动客户端延时在 7 秒左右
- 第二路 ffmpeg 在不加 `-g 25` 参数的情况下，再次请求，客户端加载时间在 2s - 10s 不等
- 第二路 ffmpeg 加上 `-g 25` 参数后，再次请求，客户端加载时间 2 s 左右

**新的问题**：一次转换的时候首次启动和非首次启动的时间差不明显，没有关注到，二次转换的时候，时间差就有点大了，这又是哪来的？

通过日志输出发现，从 ffmpeg 执行命令到有视频流输出，存在延时，第一次转换，由 rtsp 到 rtmp 输出有 1.5s 的延时，第二次转换，由 rtmp 到 rtmp 输出有 5.6s 的延时。6 s 的延时有了一个解释，但是这个延时是哪里来的呢。

官网查到的两个参数：

>probesize integer (input)  
Set probing size in bytes, i.e. the size of the data to analyze to get stream information. A higher value will enable detecting more information in case it is dispersed into the stream, but will increase latency. Must be an integer not lesser than 32. It is 5000000 by default.  
analyzeduration integer (input)  
Specify how many microseconds are analyzed to probe the input. A higher value will enable detecting more accurate information, but will increase latency. It defaults to 5,000,000 microseconds = 5 seconds.

ffmpeg 会拉取一定时间一定大小的数据，检测流的信息，检测完成后，才会转码输出。超过两个参数，任意一个就会停止。两个值值越大获取的信息越精确，相应的也会增大延时。

- ffmpeg 会花费最长 5s 的时间检测流类型
- ffmpeg 第二次转换，最坏的情况下 5s 后才开始输出流
- 但是，ffmpeg 会将 5s 前的视频进行转换，并发送给流媒体服务器
- 流媒体服务器接收到后，复制分发给客户端
- 客户端加载到视频后，开始播放的是 5s 之前的视频

通过减小两个参数的值，延时问题得到了解决，最后实况延时控制到了 3 s 左右。

至于为什么 rtsp 的检测只需要 1.5s 没有找到相关答案。
