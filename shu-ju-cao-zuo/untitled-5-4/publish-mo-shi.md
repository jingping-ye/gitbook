# Publish模式

## 1. Publish模式（订阅模式，消息被路由投递给多个队列，一个消息被多个消费者获取） <a id="publish&#x6A21;&#x5F0F;&#xFF08;&#x8BA2;&#x9605;&#x6A21;&#x5F0F;&#xFF0C;&#x6D88;&#x606F;&#x88AB;&#x8DEF;&#x7531;&#x6295;&#x9012;&#x7ED9;&#x591A;&#x4E2A;&#x961F;&#x5217;&#xFF0C;&#x4E00;&#x4E2A;&#x6D88;&#x606F;&#x88AB;&#x591A;&#x4E2A;&#x6D88;&#x8D39;&#x8005;&#x83B7;&#x53D6;&#xFF09;"></a>

* X代表交换机rabbitMQ内部组件,erlang 消息产生者是代码完成,代码的执行效率不高,消息产生者将消息放入交换机,交换机发布订阅把消息发送到所有消息队列中,对应消息队列的消费者拿到消息进行消费
* 相关场景:邮件群发,群聊天,广播\(广告\)

目录结构

kuteng-RabbitMQ

-RabbitMQ

--rabitmq.go //这个是RabbitMQ的封装

-Pub

--mainPub.go //Publish 先启动

-Sub

--mainSub.go

-Sub2

--mainSub.go

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


func NewRabbitMQPubSub(exchangeName string) *RabbitMQ {
    
    rabbitmq := NewRabbitMQ("", exchangeName, "")
    var err error
    
    rabbitmq.conn, err = amqp.Dial(rabbitmq.Mqurl)
    rabbitmq.failOnErr(err, "failed to connect rabbitmq!")
    
    rabbitmq.channel, err = rabbitmq.conn.Channel()
    rabbitmq.failOnErr(err, "failed to open a channel")
    return rabbitmq
}


func (r *RabbitMQ) PublishPub(message string) {
    
    err := r.channel.ExchangeDeclare(
        r.Exchange,
        "fanout",
        true,
        false,
        
        false,
        false,
        nil,
    )

    r.failOnErr(err, "Failed to declare an excha"+
        "nge")

    
    err = r.channel.Publish(
        r.Exchange,
        "",
        false,
        false,
        amqp.Publishing{
            ContentType: "text/plain",
            Body:        []byte(message),
        })
}


func (r *RabbitMQ) RecieveSub() {
    
    err := r.channel.ExchangeDeclare(
        r.Exchange,
        
        "fanout",
        true,
        false,
        
        false,
        false,
        nil,
    )
    r.failOnErr(err, "Failed to declare an exch"+
        "ange")
    
    q, err := r.channel.QueueDeclare(
        "", 
        false,
        false,
        true,
        false,
        nil,
    )
    r.failOnErr(err, "Failed to declare a queue")

    
    err = r.channel.QueueBind(
        q.Name,
        
        "",
        r.Exchange,
        false,
        nil)

    
    messges, err := r.channel.Consume(
        q.Name,
        "",
        true,
        false,
        false,
        false,
        nil,
    )

    forever := make(chan bool)

    go func() {
        for d := range messges {
            log.Printf("Received a message: %s", d.Body)
        }
    }()
    fmt.Println("退出请按 CTRL+C\n")
    <-forever
}
```

mainPub.go代码

```text
package main

import (
    "fmt"
    "strconv"
    "time"

    "github.com/student/kuteng-RabbitMQ/RabbitMQ"
)

func main() {
    rabbitmq := RabbitMQ.NewRabbitMQPubSub("" +
        "newProduct")
    for i := 0; i < 100; i++ {
        rabbitmq.PublishPub("订阅模式生产第" +
            strconv.Itoa(i) + "条" + "数据")
        fmt.Println("订阅模式生产第" +
            strconv.Itoa(i) + "条" + "数据")
        time.Sleep(1 * time.Second)
    }

}
```

mainSub.go代码\(两个消费者代码是一样的\)

```text
package main

import "github.com/student/kuteng-RabbitMQ/RabbitMQ"

func main() {
    rabbitmq := RabbitMQ.NewRabbitMQPubSub("" +
        "newProduct")
    rabbitmq.RecieveSub()
}
```

