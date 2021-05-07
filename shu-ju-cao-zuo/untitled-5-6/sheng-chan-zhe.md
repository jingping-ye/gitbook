# 生产者

## 1. 生产者 <a id="&#x751F;&#x4EA7;&#x8005;"></a>

运行Nsq服务集群

首先启动nsqlookud，在一个shell中，开始nsqlookupd：

$ nsqlookupd

在另一个shell中，开始nsqd：

$ nsqd --lookupd-tcp-address=127.0.0.1:4160

在另一个shell中，开始nsqadmin：

$ nsqadmin --lookupd-http-address=127.0.0.1:4161

发布初始消息（也在集群中创建主题）：

$ curl -d 'hello world 1' '[http://127.0.0.1:4151/pub?topic=test](http://127.0.0.1:4151/pub?topic=test)'

最后，在另一个shell中，开始nsq\_to\_file：

$ nsq\_to\_file --topic=test --output-dir=/tmp --lookupd-http-address=127.0.0.1:4161

验证事物按预期工作，在Web浏览器中打开[http://127.0.0.1:4171/](http://127.0.0.1:4171/) 以查看nsqadminUI并查看统计信息。另外，检查`test.*.log`写入的日志文件（）的内容/tmp。

链接nsq 并创建生产者：

```text
package main

import (
    "fmt"

    nsq "github.com/nsqio/go-nsq"
)

func main() {
    
    var producer *nsq.Producer
    
    
    producer, err := nsq.NewProducer("127.0.0.1:4150", nsq.NewConfig())
    if err != nil {
        panic(err)
    }

    err = producer.Ping()
    if nil != err {
        
        producer.Stop()
        producer = nil
    }

    fmt.Println("ping nsq success")
}
```

生产者创建topic并写入nsq：

```text
package main

import (
    "fmt"

    nsq "github.com/nsqio/go-nsq"
)

func main() {
    
    var producer *nsq.Producer
    
    
    producer, err := nsq.NewProducer("127.0.0.1:4150", nsq.NewConfig())
    if err != nil {
        panic(err)
    }

    err = producer.Ping()
    if nil != err {
        
        producer.Stop()
        producer = nil
    }

    
    topic := "test"
    for i := 0; i < 10; i++ {
        message := fmt.Sprintf("message:%d", i)
        if producer != nil && message != "" { 
            err = producer.Publish(topic, []byte(message)) 
            if err != nil {
                fmt.Printf("producer.Publish,err : %v", err)
            }
            fmt.Println(message)
        }
    }

    fmt.Println("producer.Publish success")

}
```

