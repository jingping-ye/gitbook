# 雪花算法

## 1. 雪花算法 <a id="&#x96EA;&#x82B1;&#x7B97;&#x6CD5;"></a>

### 1.1.1. 关于雪花 <a id="&#x5173;&#x4E8E;&#x96EA;&#x82B1;"></a>

雪花\(snowflake\)在自然界中，是极具独特美丽，又变幻莫测的东西：

* 1.雪花属于六方晶系，它具有四个结晶轴，其中三个辅轴在一个基面上，互相以60度的角度相交，第四轴\(主晶轴\)与三个辅轴所形成的基面垂直；
* 2.雪花的基本形状是六角形，但是大自然中却几乎找不出两朵完全相同的雪花，每一个雪花都拥有自己的独有图案，就象地球上找不出两个完全相同的人一样。许多学者用显微镜观测过成千上万朵雪花，这些研究最后表明，形状、大小完全一样和各部分完全对称的雪花，在自然界中是无法形成的。

### 1.1.2. 雪花算法 <a id="&#x96EA;&#x82B1;&#x7B97;&#x6CD5;_1"></a>

雪花算法的原始版本是scala版，用于生成分布式ID（纯数字，时间顺序）,订单编号等。

> 自增ID：对于数据敏感场景不宜使用，且不适合于分布式场景。 GUID：采用无意义字符串，数据量增大时造成访问过慢，且不宜排序。

### 1.1.3. 算法描述 <a id="&#x7B97;&#x6CD5;&#x63CF;&#x8FF0;"></a>

* 最高位是符号位，始终为0，不可用。
* 41位的时间序列，精确到毫秒级，41位的长度可以使用69年。时间位还有一个很重要的作用是可以根据时间进行排序。
* 10位的机器标识，10位的长度最多支持部署1024个节点。
* 12位的计数序列号，序列号即一系列的自增id，可以支持同一节点同一毫秒生成多个ID序号，12位的计数序列号支持每个节点每毫秒产生4096个ID序号。

代码：

```text
package main

import (
    "errors"
    "fmt"
    "sync"
    "time"
)

const (
    workerBits  uint8 = 10
    numberBits  uint8 = 12
    workerMax   int64 = -1 ^ (-1 << workerBits)
    numberMax   int64 = -1 ^ (-1 << numberBits)
    timeShift   uint8 = workerBits + numberBits
    workerShift uint8 = numberBits
    startTime   int64 = 1525705533000 
)

type Worker struct {
    mu        sync.Mutex
    timestamp int64
    workerId  int64
    number    int64
}

func NewWorker(workerId int64) (*Worker, error) {
    if workerId < 0 || workerId > workerMax {
        return nil, errors.New("Worker ID excess of quantity")
    }
    
    return &Worker{
        timestamp: 0,
        workerId:  workerId,
        number:    0,
    }, nil
}

func (w *Worker) GetId() int64 {
    w.mu.Lock()
    defer w.mu.Unlock()
    now := time.Now().UnixNano() / 1e6
    if w.timestamp == now {
        w.number++
        if w.number > numberMax {
            for now <= w.timestamp {
                now = time.Now().UnixNano() / 1e6
            }
        }
    } else {
        w.number = 0
        w.timestamp = now
    }
    ID := int64((now-startTime)<<timeShift | (w.workerId << workerShift) | (w.number))
    return ID
}
func main() {
    
    node, err := NewWorker(1)
    if err != nil {
        panic(err)
    }
    for {
        fmt.Println(node.GetId())
    }
}
```

