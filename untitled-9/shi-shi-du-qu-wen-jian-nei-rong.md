# 实时读取文件内容

## 1. 实时读取文件内容 <a id="&#x5B9E;&#x65F6;&#x8BFB;&#x53D6;&#x6587;&#x4EF6;&#x5185;&#x5BB9;"></a>

在做日志分析的时候，需要实时的获取日志里面的内容找到了tail感觉好不错分享给大家

```text
package main

import (
    "fmt"
    "time"

    "github.com/hpcloud/tail"
)

func main() {
    fileName := "./my.log"
    config := tail.Config{
        ReOpen:    true,                                 
        Follow:    true,                                 
        Location:  &tail.SeekInfo{Offset: 0, Whence: 2}, 
        MustExist: false,                                
        Poll:      true,
    }
    tails, err := tail.TailFile(fileName, config)
    if err != nil {
        fmt.Println("tail file failed, err:", err)
        return
    }
    var (
        line *tail.Line
        ok   bool
    )
    for {
        line, ok = <-tails.Lines
        if !ok {
            fmt.Printf("tail file close reopen, filename:%s\n", tails.Filename)
            time.Sleep(time.Second)
            continue
        }
        fmt.Println("line:", line.Text)
    }
}
```

在同级目录下面定义一个my.log文件，在文件里面写入文字敲下回车，并且保存之后，程序会自动的获取并且打印，可以根据业务需要就行修改

