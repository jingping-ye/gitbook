# Topic模式

## 1. Topic模式（话题模式，一个消息被多个消费者获取，消息的目标queue可用BindingKey以通配符，（\#：一个或多个词，\*：一个词）的方式指定 <a id="topic&#x6A21;&#x5F0F;&#xFF08;&#x8BDD;&#x9898;&#x6A21;&#x5F0F;&#xFF0C;&#x4E00;&#x4E2A;&#x6D88;&#x606F;&#x88AB;&#x591A;&#x4E2A;&#x6D88;&#x8D39;&#x8005;&#x83B7;&#x53D6;&#xFF0C;&#x6D88;&#x606F;&#x7684;&#x76EE;&#x6807;queue&#x53EF;&#x7528;bindingkey&#x4EE5;&#x901A;&#x914D;&#x7B26;&#xFF0C;&#xFF08;&#xFF1A;&#x4E00;&#x4E2A;&#x6216;&#x591A;&#x4E2A;&#x8BCD;&#xFF0C;&#xFF1A;&#x4E00;&#x4E2A;&#x8BCD;&#xFF09;&#x7684;&#x65B9;&#x5F0F;&#x6307;&#x5B9A;"></a>

* 星号井号代表通配符
* 星号代表多个单词,井号代表一个单词
* 路由功能添加模糊匹配
* 消息产生者产生消息,把消息交给交换机
* 交换机根据key的规则模糊匹配到对应的队列,由队列的监听消费者接收消息消费

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



func NewRabbitMQTopic(exchangeName string, routingKey string) *RabbitMQ {
    
    rabbitmq := NewRabbitMQ("", exchangeName, routingKey)
    var err error
    
    rabbitmq.conn, err = amqp.Dial(rabbitmq.Mqurl)
    rabbitmq.failOnErr(err, "failed to connect rabbitmq!")
    
    rabbitmq.channel, err = rabbitmq.conn.Channel()
    rabbitmq.failOnErr(err, "failed to open a channel")
    return rabbitmq
}


func (r *RabbitMQ) PublishTopic(message string) {
    
    err := r.channel.ExchangeDeclare(
        r.Exchange,
        
        "topic",
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





func (r *RabbitMQ) RecieveTopic() {
    
    err := r.channel.ExchangeDeclare(
        r.Exchange,
        
        "topic",
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
    kutengOne := RabbitMQ.NewRabbitMQTopic("exKutengTopic", "kuteng.topic.one")
    kutengTwo := RabbitMQ.NewRabbitMQTopic("exKutengTopic", "kuteng.topic.two")
    for i := 0; i <= 100; i++ {
        kutengOne.PublishTopic("Hello kuteng topic one!" + strconv.Itoa(i))
        kutengTwo.PublishTopic("Hello kuteng topic Two!" + strconv.Itoa(i))
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
    kutengOne := RabbitMQ.NewRabbitMQTopic("exKutengTopic", "#")
    kutengOne.RecieveTopic()
}
```

recieve2/mainrecieve.go代码

```text
package main

import "github.com/student/kuteng-RabbitMQ/RabbitMQ"

func main() {
    kutengOne := RabbitMQ.NewRabbitMQTopic("exKutengTopic", "kuteng.*.two")
    kutengOne.RecieveTopic()
}
```

