# Channel-地鼠文档

## 1. 前言 <a id="sqx82"></a>

channel一般用于协程之间的通信，channel也可以用于并发控制。比如主协程启动N个子协程，主协程等待所有子协程退出后再继续后续流程，这种场景下channel也可轻易实现。

## 2. 场景示例 <a id="b6kbu9"></a>

下面程序展示一个使用channel控制子协程的例子：

```text
package main

import (
    "time"
    "fmt"
)

func Process(ch chan int) {
    
    time.Sleep(time.Second)

    ch <- 1 
}

func main() {
    channels := make([]chan int, 10) 

    for i:= 0; i < 10; i++ {
        channels[i] = make(chan int) 
        go Process(channels[i])      
    }

    for i, ch := range channels {  
        <-ch
        fmt.Println("Routine ", i, " quit!")
    }
}
```

上面程序通过创建N个channel来管理N个协程，每个协程都有一个channel用于跟父协程通信，父协程创建完所有协程后等待所有协程结束。

这个例子中，父协程仅仅是等待子协程结束，其实父协程也可以向管道中写入数据通知子协程结束，这时子协程需要定期地探测管道中是否有消息出现。

## 3. 总结 <a id="bpowf3"></a>

使用channel来控制子协程的优点是实现简单，缺点是当需要大量创建协程时就需要有相同数量的channel，而且对于子协程继续派生出来的协程不方便控制。

后面继续介绍的WaitGroup、Context看起来比channel优雅一些，在各种开源组件中使用频率比channel高得多。

文档更新时间: 2020-08-08 21:59   作者：kuteng

