# Work模式

## 1. Work模式（工作模式，一个消息只能被一个消费者获取） <a id="work&#x6A21;&#x5F0F;&#xFF08;&#x5DE5;&#x4F5C;&#x6A21;&#x5F0F;&#xFF0C;&#x4E00;&#x4E2A;&#x6D88;&#x606F;&#x53EA;&#x80FD;&#x88AB;&#x4E00;&#x4E2A;&#x6D88;&#x8D39;&#x8005;&#x83B7;&#x53D6;&#xFF09;"></a>

* 消息产生者将消息放入队列消费者可以有多个,消费者1,消费者2,同时监听同一个队列,消息被消费?C1 C2共同争抢当前的消息队列内容,谁先拿到谁负责消费消息\(隐患,高并发情况下,默认会产生某一个消息被多个消费者共同使用,可以设置一个开关\(syncronize,与同步锁的性能不一样\) 保证一条消息只能被一个消费者使用\)
* 应用场景:红包;大项目中的资源调度\(任务分配系统不需知道哪一个任务执行系统在空闲,直接将任务扔到消息队列中,空闲的系统自动争抢\)

目录结构

kuteng-RabbitMQ

-RabbitMQ

--rabitmq.go //这个是RabbitMQ的封装和Simple模式代码一样

-SimlpePublish

--mainSimlpePublish.go //Publish 先启动

-SimpleRecieve1

--mainSimpleRecieve.go

-SimpleRecieve2

--mainSimpleRecieve.go

**注意**

Work模式和Simple模式相比代码并没有发生变化只是多了一个消费者

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
    "strconv"
    "time"

    "github.com/student/kuteng-RabbitMQ/RabbitMQ"
)

func main() {
    rabbitmq := RabbitMQ.NewRabbitMQSimple("" +
        "kuteng")

    for i := 0; i <= 100; i++ {
        rabbitmq.PublishSimple("Hello kuteng!" + strconv.Itoa(i))
        time.Sleep(1 * time.Second)
        fmt.Println(i)
    }
}
```

mainSimpleRecieve.go代码\(两个消费端的代码是一样的\)

```text
package main

import "github.com/student/kuteng-RabbitMQ/RabbitMQ"

func main() {
    rabbitmq := RabbitMQ.NewRabbitMQSimple("" +
        "kuteng")
    rabbitmq.ConsumeSimple()
}
```

