# 操作Kafka

## 1. 操作Kafka <a id="&#x64CD;&#x4F5C;kafka"></a>

### 1.1.1. sarama <a id="sarama"></a>

Go语言中连接kafka使用第三方库: github.com/Shopify/sarama。

### 1.1.2. 下载及安装 <a id="&#x4E0B;&#x8F7D;&#x53CA;&#x5B89;&#x88C5;"></a>

```text
    go get github.com/Shopify/sarama
```

注意事项: sarama v1.20之后的版本加入了zstd压缩算法，需要用到cgo，在Windows平台编译时会提示类似如下错误： github.com/DataDog/zstd exec: "gcc":executable file not found in %PATH% 所以在Windows平台请使用v1.19版本的sarama。\(如果不会版本控制请查看博客里面的go module章节\)

### 1.1.3. 连接kafka发送消息 <a id="&#x8FDE;&#x63A5;kafka&#x53D1;&#x9001;&#x6D88;&#x606F;"></a>

```text
package main

import (
    "fmt"

    "github.com/Shopify/sarama"
)



func main() {
    config := sarama.NewConfig()
    config.Producer.RequiredAcks = sarama.WaitForAll          
    config.Producer.Partitioner = sarama.NewRandomPartitioner 
    config.Producer.Return.Successes = true                   

    
    msg := &sarama.ProducerMessage{}
    msg.Topic = "web_log"
    msg.Value = sarama.StringEncoder("this is a test log")
    
    client, err := sarama.NewSyncProducer([]string{"127.0.0.1:9092"}, config)
    if err != nil {
        fmt.Println("producer closed, err:", err)
        return
    }
    defer client.Close()
    
    pid, offset, err := client.SendMessage(msg)
    if err != nil {
        fmt.Println("send msg failed, err:", err)
        return
    }
    fmt.Printf("pid:%v offset:%v\n", pid, offset)
}
```

### 1.1.4. 连接kafka消费消息 <a id="&#x8FDE;&#x63A5;kafka&#x6D88;&#x8D39;&#x6D88;&#x606F;"></a>

```text
package main

import (
    "fmt"

    "github.com/Shopify/sarama"
)



func main() {
    consumer, err := sarama.NewConsumer([]string{"127.0.0.1:9092"}, nil)
    if err != nil {
        fmt.Printf("fail to start consumer, err:%v\n", err)
        return
    }
    partitionList, err := consumer.Partitions("web_log") 
    if err != nil {
        fmt.Printf("fail to get list of partition:err%v\n", err)
        return
    }
    fmt.Println(partitionList)
    for partition := range partitionList { 
        
        pc, err := consumer.ConsumePartition("web_log", int32(partition), sarama.OffsetNewest)
        if err != nil {
            fmt.Printf("failed to start consumer for partition %d,err:%v\n", partition, err)
            return
        }
        defer pc.AsyncClose()
        
        go func(sarama.PartitionConsumer) {
            for msg := range pc.Messages() {
                fmt.Printf("Partition:%d Offset:%d Key:%v Value:%v", msg.Partition, msg.Offset, msg.Key, msg.Value)
            }
        }(pc)
    }
}
```

