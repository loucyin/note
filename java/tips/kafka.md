# kafka

## 什么鬼
> Kafka是一种高吞吐量的分布式发布订阅消息系统，它可以处理消费者规模的网站中的所有动作流数据。这种动作（网页浏览，搜索和其他用户的行动）是在现代网络上的许多社会功能的一个关键因素。 这些数据通常是由于吞吐量的要求而通过处理日志和日志聚合来解决。 对于像Hadoop的一样的日志数据和离线分析系统，但又要求实时处理的限制，这是一个可行的解决方案。Kafka的目的是通过Hadoop的并行加载机制来统一线上和离线的消息处理，也是为了通过集群来提供实时的消费。

它提供了类似于 JMS（Java Message Service） 的特性，但是在设计实现上完全不同，此外它并不是JMS规范的实现。kafka 对消息保存时根据Topic进行归类，发送消息者成为 Producer ,消息接受者成为 Consumer ,此外 kafka 集群有多个 kafka 实例组成，每个实例(server)成为 broker。无论是 kafka 集群，还是 producer 和 consumer 都依赖于 zookeeper 来保证系统可用性集群保存一些 meta 信息。

## 用途

- 构建可在系统或应用程序之间可靠获取数据的实时流数据流水线
- 构建对数据流进行变换或反应的实时流应用程序

## 4 个核心 API

- Producer API 将记录发布到一个或多个主题
- Consumer API 从一个或多个主题消费一条记录
- Streams API 从一个或多个主题消费一条记录，并发布记录到一个或多个主题
- Connector API 允许构建和运行复用那些连接 kafka 主题到已经存在的应用或者数据系统的生产者或消费者。

![Kafuka API](./image/kafka-apis.png)
图片来自官网

## 命令
- create topic
```bash
bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test
```
- list topic
```bash
bin/kafka-topics.sh --list --zookeeper localhost:2181
```

- send message

```bash
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test
```
输入消息回车发送

- start consumer
```bash
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning
```

## 下载

[下载地址](http://kafka.apache.org/downloads)

下载解压，下载的版本要和使用的 jar 包版本相对应。

## 启动
windows 下启动，先启动 zookeeper ，再启动 kafka-server，下面是自己写的一个 bat 文件：

```bat
@echo off
start cmd /k "D:\kafka_2.10-0.10.2.0\bin\windows\zookeeper-server-start.bat D:\kafka_2.10-0.10.2.0\config\zookeeper.properties"
start cmd /k "D:\kafka_2.10-0.10.2.0\bin\windows\kafka-server-start.bat D:\kafka_2.10-0.10.2.0\config\server.properties"
```

## gradle 依赖

```grovvy
compile group: 'org.slf4j', name: 'slf4j-api', version: '1.7.21'
compile group: 'log4j', name: 'log4j', version: '1.2.17'
compile group: 'org.apache.kafka', name: 'kafka-clients', version: '0.10.2.0'
compile group: 'org.apache.kafka', name: 'kafka_2.10', version: '0.10.2.0'
compile group: 'org.apache.kafka', name: 'kafka-streams', version: '0.10.2.0'
```

## Producer

```java
import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.ProducerConfig;
import org.apache.kafka.clients.producer.ProducerRecord;

import java.util.Properties;

public class ProducerDemo {
    public final static String TOPIC="TEST-TOPIC";

    public  static  void main(String[] args){
        Properties props=new Properties();
        props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG,"127.0.0.1:9092");
        props.put(ProducerConfig.ACKS_CONFIG, "all");
        props.put(ProducerConfig.RETRIES_CONFIG, 0);
        props.put(ProducerConfig.BATCH_SIZE_CONFIG, 16384);
        props.put(ProducerConfig.LINGER_MS_CONFIG, 1);
        props.put(ProducerConfig.BUFFER_MEMORY_CONFIG, 33554432);
        props.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, "org.apache.kafka.common.serialization.StringSerializer");
        props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, "org.apache.kafka.common.serialization.StringSerializer");
        KafkaProducer<String,String> producer=new KafkaProducer<String, String>(props) ;

        int messageCount = 0;

        while (messageCount < 10){
            producer.send(new ProducerRecord<String, String>(TOPIC,
                    String.format("key %d",messageCount),
                    String.format("hello message %d",messageCount)));

            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            messageCount ++;
        }
    }

}
```

## Consumer

```java
import org.apache.kafka.clients.consumer.ConsumerConfig;
import org.apache.kafka.clients.consumer.ConsumerRecord;
import org.apache.kafka.clients.consumer.ConsumerRecords;
import org.apache.kafka.clients.consumer.KafkaConsumer;

import java.util.Arrays;
import java.util.Collections;
import java.util.Properties;

public class ConsumerDemo {
    public final static String TOPIC="TEST-TOPIC";
    public  static  void main(String[] args){
        Properties props = new Properties();

        props.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
        props.put(ConsumerConfig.GROUP_ID_CONFIG, "test");
        props.put(ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG, "true");
        props.put(ConsumerConfig.AUTO_COMMIT_INTERVAL_MS_CONFIG, "1000");
        props.put(ConsumerConfig.SESSION_TIMEOUT_MS_CONFIG, "30000");
        props.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, "org.apache.kafka.common.serialization.StringDeserializer");
        props.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, "org.apache.kafka.common.serialization.StringDeserializer");

        KafkaConsumer<String,String> consumer = new KafkaConsumer<>(props);
        consumer.subscribe(Collections.singletonList(TOPIC));

        while (true){
            ConsumerRecords<String,String> records = consumer.poll(100L);
            for (ConsumerRecord<String,String> record:records){
                System.out.printf("offset = %d, key = %s, value = %s", record.offset(), record.key(), record.value());
            }
        }
    }

}
```

**运行程序之前，先启动 kafka。**

## stream
### 两种类型
- KStream
> 在一个流中(KStream)，每个key-value是一个独立的信息片断，比如，用户购买流是：alice->黄油，bob->面包，alice->奶酪面包，我们知道alice既买了黄油，又买了奶酪面包。

- KTable
> 对于一个表table( KTable)，是代表一个变化日志，如果表包含两对同样key的key-value值，后者会覆盖前面的记录，因为key值一样的，比如用户地址表：alice -> 纽约, bob -> 旧金山, alice -> 芝加哥，意味着Alice从纽约迁移到芝加哥，而不是同时居住在两个地方。

```java
public static void main(String[] args){
    Map<String, Object> props = new HashMap<>();
    props.put(StreamsConfig.APPLICATION_ID_CONFIG, "jd_group");
    props.put(StreamsConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
    props.put(StreamsConfig.KEY_SERDE_CLASS_CONFIG,Serdes.String().getClass());
    props.put(StreamsConfig.VALUE_SERDE_CLASS_CONFIG,Serdes.String().getClass());

    StreamsConfig config = new StreamsConfig(props);

    KStreamBuilder builder = new KStreamBuilder();

    KStream<String,String> kStream = builder.stream("topic1");
    kStream.mapValues(new ValueMapper<String, String>() {
        @Override
        public String apply(String value) {
            return value==null?"0":value.length()+"";
        }
    })
    .to("TEST-TOPIC");

    KafkaStreams streams = new KafkaStreams(builder, config);

    streams.start();

}
```

上面的 KStream 消费一个 topic1 的一条记录，将记录的文本长度发送到 TEST-TOPIC 。

## 笔记
- kafka 集群可以通过配置保留期限保留所有发布的记录，不管是否被消费
- 每条记录都会有一个偏移量，偏移量由消费者控制，通常消费者读取记录时线性提高祁偏移量；消费者也可以通过控制偏移量消费之前的记录

## 参考链接
- [百度百科](http://baike.baidu.com/link?url=swNHcRq-sjnH9FbPm3cmYTl0KZ8fGPkr6YTk7pxenXm8KRBb2Pxje
TiBgIaHL0MNMW7jT7RqIx0-jssyZP2Wgq)
- [kafka java demo](http://wandejun1012.iteye.com/blog/2310349)
- [入门](http://www.cnblogs.com/likehua/p/3999538.html)
- [Kafka Introduction](http://kafka.apache.org/intro)
- [KafkaStream简介](http://www.tuicool.com/articles/yMbmY3e)
