# gocron

## 1. gocron <a id="gocron"></a>

### 1.1.1. jasonlvhit/gocron <a id="jasonlvhitgocron"></a>

**安装：**

```text
go get -u github.com/jasonlvhit/gocron
```

每隔1秒执行一个任务，每隔4秒执行另一个任务：

```text
package main

import (
    "fmt"
    "time"

    "github.com/jasonlvhit/gocron"
)

func task() {
    fmt.Println("I am runnning task.", time.Now())
}
func superWang() {
    fmt.Println("I am runnning superWang.", time.Now())
}

func main() {
    s := gocron.NewScheduler()
    s.Every(1).Seconds().Do(task)
    s.Every(4).Seconds().Do(superWang)

    sc := s.Start() 
    go test(s, sc)  
    <-sc            
}

func test(s *gocron.Scheduler, sc chan bool) {
    time.Sleep(8 * time.Second)
    s.Remove(task) 
    time.Sleep(8 * time.Second)
    s.Clear()
    fmt.Println("All task removed")
    close(sc) 
}
```

输出结果：

```text
    I am runnning task. 2019-11-15 09:37:07.218128145 +0800 CST m=+1.000414542
    I am runnning task. 2019-11-15 09:37:08.218062127 +0800 CST m=+2.000348513
    I am runnning task. 2019-11-15 09:37:09.218047138 +0800 CST m=+3.000333512
    I am runnning superWang. 2019-11-15 09:37:10.218058701 +0800 CST m=+4.000345070
    I am runnning task. 2019-11-15 09:37:10.218100706 +0800 CST m=+4.000387079
    I am runnning task. 2019-11-15 09:37:11.218033085 +0800 CST m=+5.000319455
    I am runnning task. 2019-11-15 09:37:12.21804561 +0800 CST m=+6.000331978
    I am runnning task. 2019-11-15 09:37:13.218053083 +0800 CST m=+7.000339456
    I am runnning superWang. 2019-11-15 09:37:14.218038711 +0800 CST m=+8.000325086
    I am runnning superWang. 2019-11-15 09:37:18.218061128 +0800 CST m=+12.000347516
    I am runnning superWang. 2019-11-15 09:37:22.218054256 +0800 CST m=+16.000340629
    All task removed
```

