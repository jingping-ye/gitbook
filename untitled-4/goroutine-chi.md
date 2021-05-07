# Goroutine池

## 1. Goroutine池 <a id="goroutine&#x6C60;"></a>

### 1.1.1. worker pool（goroutine池） <a id="worker-pool&#xFF08;goroutine&#x6C60;&#xFF09;"></a>

* 本质上是生产者消费者模型
* 可以有效控制goroutine数量，防止暴涨
* 需求：
  * 计算一个数字的各个位数之和，例如数字123，结果为1+2+3=6
  * 随机生成数字进行计算
* 控制台输出结果如下：

```text
package main

import (
    "fmt"
    "math/rand"
)

type Job struct {
    
    Id int
    
    RandNum int
}

type Result struct {
    
    job *Job
    
    sum int
}

func main() {
    
    
    jobChan := make(chan *Job, 128)
    
    resultChan := make(chan *Result, 128)
    
    createPool(64, jobChan, resultChan)
    
    go func(resultChan chan *Result) {
        
        for result := range resultChan {
            fmt.Printf("job id:%v randnum:%v result:%d\n", result.job.Id,
                result.job.RandNum, result.sum)
        }
    }(resultChan)
    var id int
    
    for {
        id++
        
        r_num := rand.Int()
        job := &Job{
            Id:      id,
            RandNum: r_num,
        }
        jobChan <- job
    }
}



func createPool(num int, jobChan chan *Job, resultChan chan *Result) {
    
    for i := 0; i < num; i++ {
        go func(jobChan chan *Job, resultChan chan *Result) {
            
            
            for job := range jobChan {
                
                r_num := job.RandNum
                
                
                var sum int
                for r_num != 0 {
                    tmp := r_num % 10
                    sum += tmp
                    r_num /= 10
                }
                
                r := &Result{
                    job: job,
                    sum: sum,
                }
                
                resultChan <- r
            }
        }(jobChan, resultChan)
    }
}
```

