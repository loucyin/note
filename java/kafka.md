# kafka

## 什么鬼
> Kafka是一种高吞吐量的分布式发布订阅消息系统，它可以处理消费者规模的网站中的所有动作流数据。这种动作（网页浏览，搜索和其他用户的行动）是在现代网络上的许多社会功能的一个关键因素。 这些数据通常是由于吞吐量的要求而通过处理日志和日志聚合来解决。 对于像Hadoop的一样的日志数据和离线分析系统，但又要求实时处理的限制，这是一个可行的解决方案。Kafka的目的是通过Hadoop的并行加载机制来统一线上和离线的消息处理，也是为了通过集群来提供实时的消费。

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

## Producer

```java
import kafka.javaapi.producer.Producer;
import kafka.producer.KeyedMessage;
import kafka.producer.ProducerConfig;

import java.util.Properties;

public class KafkaProducer {

    private  final Producer<String,String> producer;

    public final static String TOPIC="TEST-TOPIC";

    private  KafkaProducer(){
        Properties props=new Properties();
        props.put("metadata.broker.list","127.0.0.1:9092");
        props.put("serializer.class","kafka.serializer.StringEncoder");
        props.put("key.serializer.class","kafka.serializer.StringEncoder");
        props.put("request.required.acks","-1");
        producer=new Producer<String, String>(new ProducerConfig(props)) ;
    }


    void produce(){
        int messageNo=1000;
        final int COUNT=10;
        while(messageNo<COUNT){
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            String key=String.valueOf(messageNo);
            String data="hello kafka message"+key;
            producer.send(new KeyedMessage<String,String>(TOPIC,key,data));
            System.out.println(data);
            messageNo++;
        }
    }

    public  static  void main(String[] args){
        new KafkaProducer().produce();
    }

}
```

## Consumer

```java
import kafka.consumer.ConsumerConfig;
import kafka.consumer.ConsumerIterator;
import kafka.consumer.KafkaStream;
import kafka.javaapi.consumer.ConsumerConnector;
import kafka.serializer.StringDecoder;
import kafka.utils.VerifiableProperties;

import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.Properties;

public class KafkaConsumer {


    private  final ConsumerConnector consumer;

    public final static String TOPIC="TEST-TOPIC";

    private KafkaConsumer(){
        Properties props=new Properties();
        props.put("zookeeper.connect","127.0.0.1:2181");
        props.put("group.id","jd-group");//消费组是什么概念？

        props.put("zookeeper.session.timeout.ms","60000");
        props.put("zookeeper.sync.time.ms","200");
        props.put("auto.commit.interval.ms","1000");
        props.put("auto.offset.reset","smallest");

        props.put("serializer.class","kafka.serializer.StringEncoder");

        ConsumerConfig config=new ConsumerConfig(props);

        consumer=kafka.consumer.Consumer.createJavaConsumerConnector(config);
    }


    void consume(){
        Map<String,Integer> topicCountMap=new HashMap<String, Integer>();
        topicCountMap.put(KafkaConsumer.TOPIC,new Integer(1));
        StringDecoder keyDecoder=new StringDecoder(new VerifiableProperties());
        StringDecoder valueDecoder=new StringDecoder(new VerifiableProperties());

        Map<String,List<KafkaStream<String,String>>> consumerMap=
                consumer.createMessageStreams(topicCountMap,keyDecoder,valueDecoder);

        KafkaStream<String,String> stream=consumerMap.get(KafkaConsumer.TOPIC).get(0);
        ConsumerIterator<String,String> it=stream.iterator();
        while(it.hasNext()){
            System.out.println(it.next().message());
        }
    }


    public  static  void main(String[] args){
        new KafkaConsumer().consume();
    }

}
```

**运行程序之前，先启动 kafka。**

## 参考链接
- [百度百科](http://baike.baidu.com/link?url=swNHcRq-sjnH9FbPm3cmYTl0KZ8fGPkr6YTk7pxenXm8KRBb2Pxje
TiBgIaHL0MNMW7jT7RqIx0-jssyZP2Wgq)
- [kafka java demo](http://wandejun1012.iteye.com/blog/2310349)
