# Simple模式

## 1. Simple模式 <a id="simple&#x6A21;&#x5F0F;"></a>

* 消息产生着§将消息放入队列
* 消息的消费者\(consumer\) 监听\(while\) 消息队列,如果队列中有消息,就消费掉,消息被拿走后,自动从队列中删除\(隐患 消息可能没有被消费者正确处理,已经从队列中消失了,造成消息的丢失\)应用场景:聊天\(中间有一个过度的服务器;p端,c端\)

做simple简单模式之前首先我们新建一个`Virtual Host`并且给他分配一个用户名，用来隔离数据，根据自己需要自行创建

**第一步**

**第二步**

**第三步**

**第四步**

**第五步**

### 1.1.1. 代码层面 <a id="&#x4EE3;&#x7801;&#x5C42;&#x9762;"></a>

目录结构

kuteng-RabbitMQ

-RabbitMQ

--rabitmq.go //这个是RabbitMQ的封装

-SimlpePublish

--mainSimlpePublish.go //Publish 先启动

-SimpleRecieve

--mainSimpleRecieve.go

rabitmq.go代码

```text
package RabbitMQ

import (
    "fmt"
    "log"

    "github.com/streadway/amqp"
)


const MQURL = "amqp://kuteng:kuteng@127.0.0.1:5672/kuteng"


type RabbitMQ struct {
    conn    *amqp.Connection
    channel *amqp.Channel
    
    QueueName string
    
    Exchange string
    
    Key string
    
    Mqurl string
}


func NewRabbitMQ(queueName string, exchange string, key string) *RabbitMQ {
    return &RabbitMQ{QueueName: queueName, Exchange: exchange, Key: key, Mqurl: MQURL}
}


func (r *RabbitMQ) Destory() {
    r.channel.Close()
    r.conn.Close()
}


func (r *RabbitMQ) failOnErr(err error, message string) {
    if err != nil {
        log.Fatalf("%s:%s", message, err)
        panic(fmt.Sprintf("%s:%s", message, err))
    }
}


func NewRabbitMQSimple(queueName string) *RabbitMQ {
    
    rabbitmq := NewRabbitMQ(queueName, "", "")
    var err error
    
    rabbitmq.conn, err = amqp.Dial(rabbitmq.Mqurl)
    rabbitmq.failOnErr(err, "failed to connect rabb"+
        "itmq!")
    
    rabbitmq.channel, err = rabbitmq.conn.Channel()
    rabbitmq.failOnErr(err, "failed to open a channel")
    return rabbitmq
}


func (r *RabbitMQ) PublishSimple(message string) {
    
    _, err := r.channel.QueueDeclare(
        r.QueueName,
        
        false,
        
        false,
        
        false,
        
        false,
        
        nil,
    )
    if err != nil {
        fmt.Println(err)
    }
    
    r.channel.Publish(
        r.Exchange,
        r.QueueName,
        
        false,
        
        false,
        amqp.Publishing{
            ContentType: "text/plain",
            Body:        []byte(message),
        })
}


func (r *RabbitMQ) ConsumeSimple() {
    
    q, err := r.channel.QueueDeclare(
        r.QueueName,
        
        false,
        
        false,
        
        false,
        
        false,
        
        nil,
    )
    if err != nil {
        fmt.Println(err)
    }

    
    msgs, err := r.channel.Consume(
        q.Name, 
        
        "", 
        
        true, 
        
        false, 
        
        false, 
        
        false, 
        nil,   
    )
    if err != nil {
        fmt.Println(err)
    }

    forever := make(chan bool)
    
    go func() {
        for d := range msgs {
            
            log.Printf("Received a message: %s", d.Body)

        }
    }()

    log.Printf(" [*] Waiting for messages. To exit press CTRL+C")
    <-forever

}
```

mainSimlpePublish.go代码

```text
package main

import (
    "fmt"

    "github.com/student/kuteng-RabbitMQ/RabbitMQ"
)

func main() {
    rabbitmq := RabbitMQ.NewRabbitMQSimple("" +
        "kuteng")
    rabbitmq.PublishSimple("Hello kuteng222!")
    fmt.Println("发送成功！")
}
```

mainSimpleRecieve.go代码

```text
package main

import "github.com/student/kuteng-RabbitMQ/RabbitMQ"

func main() {
    rabbitmq := RabbitMQ.NewRabbitMQSimple("" +
        "kuteng")
    rabbitmq.ConsumeSimple()
}
```

