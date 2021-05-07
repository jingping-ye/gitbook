# Routing模式

## 1. Routing模式\(路由模式，一个消息被多个消费者获取，并且消息的目标队列可被生产者指定\) <a id="routing&#x6A21;&#x5F0F;&#x8DEF;&#x7531;&#x6A21;&#x5F0F;&#xFF0C;&#x4E00;&#x4E2A;&#x6D88;&#x606F;&#x88AB;&#x591A;&#x4E2A;&#x6D88;&#x8D39;&#x8005;&#x83B7;&#x53D6;&#xFF0C;&#x5E76;&#x4E14;&#x6D88;&#x606F;&#x7684;&#x76EE;&#x6807;&#x961F;&#x5217;&#x53EF;&#x88AB;&#x751F;&#x4EA7;&#x8005;&#x6307;&#x5B9A;"></a>

* 消息生产者将消息发送给交换机按照路由判断,路由是字符串\(info\) 当前产生的消息携带路由字符\(对象的方法\),交换机根据路由的key,只能匹配上路由key对应的消息队列,对应的消费者才能消费消息;
* 根据业务功能定义路由字符串
* 从系统的代码逻辑中获取对应的功能字符串,将消息任务扔到对应的队列中业务场景:error 通知;EXCEPTION;错误通知的功能;传统意义的错误通知;客户通知;利用key路由,可以将程序中的错误封装成消息传入到消息队列中,开发者可以自定义消费者,实时接收错误;

目录结构

kuteng-RabbitMQ

-RabbitMQ

--rabitmq.go //这个是RabbitMQ的封装

-publish

--mainpublish.go //Publish 先启动

-recieve1

--mainrecieve.go

-recieve2

--mainrecieve.go

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



func NewRabbitMQRouting(exchangeName string, routingKey string) *RabbitMQ {
    
    rabbitmq := NewRabbitMQ("", exchangeName, routingKey)
    var err error
    
    rabbitmq.conn, err = amqp.Dial(rabbitmq.Mqurl)
    rabbitmq.failOnErr(err, "failed to connect rabbitmq!")
    
    rabbitmq.channel, err = rabbitmq.conn.Channel()
    rabbitmq.failOnErr(err, "failed to open a channel")
    return rabbitmq
}


func (r *RabbitMQ) PublishRouting(message string) {
    
    err := r.channel.ExchangeDeclare(
        r.Exchange,
        
        "direct",
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
        
        r.Key,
        false,
        false,
        amqp.Publishing{
            ContentType: "text/plain",
            Body:        []byte(message),
        })
}


func (r *RabbitMQ) RecieveRouting() {
    
    err := r.channel.ExchangeDeclare(
        r.Exchange,
        
        "direct",
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
        
        r.Key,
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

mainpublish.go代码

```text
package main

import (
    "fmt"
    "strconv"
    "time"

    "github.com/student/kuteng-RabbitMQ/RabbitMQ"
)

func main() {
    kutengone := RabbitMQ.NewRabbitMQRouting("kuteng", "kuteng_one")
    kutengtwo := RabbitMQ.NewRabbitMQRouting("kuteng", "kuteng_two")
    for i := 0; i <= 100; i++ {
        kutengone.PublishRouting("Hello kuteng one!" + strconv.Itoa(i))
        kutengtwo.PublishRouting("Hello kuteng Two!" + strconv.Itoa(i))
        time.Sleep(1 * time.Second)
        fmt.Println(i)
    }

}
```

recieve1/mainrecieve.go代码

```text
package main

import "github.com/student/kuteng-RabbitMQ/RabbitMQ"

func main() {
    kutengone := RabbitMQ.NewRabbitMQRouting("kuteng", "kuteng_one")
    kutengone.RecieveRouting()
}
```

recieve2/mainrecieve.go代码

```text
package main

import "github.com/student/kuteng-RabbitMQ/RabbitMQ"

func main() {
    kutengtwo := RabbitMQ.NewRabbitMQRouting("kuteng", "kuteng_two")
    kutengtwo.RecieveRouting()
}
```

