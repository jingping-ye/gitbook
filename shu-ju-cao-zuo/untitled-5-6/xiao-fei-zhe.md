# 消费者

## 1. 消费者 <a id="&#x6D88;&#x8D39;&#x8005;"></a>

```text

package main

import (
    "fmt"
    "time"

    "github.com/nsqio/go-nsq"
)


type ConsumerT struct{}


func main() {
    InitConsumer("test", "test-channel", "127.0.0.1:4161")
    for {
        time.Sleep(time.Second * 10)
    }
}


func (*ConsumerT) HandleMessage(msg *nsq.Message) error {
    fmt.Println("receive", msg.NSQDAddress, "message:", string(msg.Body))
    return nil
}


func InitConsumer(topic string, channel string, address string) {
    cfg := nsq.NewConfig()
    cfg.LookupdPollInterval = time.Second          
    c, err := nsq.NewConsumer(topic, channel, cfg) 
    if err != nil {
        panic(err)
    }
    c.SetLogger(nil, 0)        
    c.AddHandler(&ConsumerT{}) 

    
    if err := c.ConnectToNSQLookupd(address); err != nil {
        panic(err)
    }

    
    
    
    

    
    
    
    
}
```

