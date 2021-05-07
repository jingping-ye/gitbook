# 链接Redis

* [**1.** 链接Redis](lian-jie-redis.md#链接redis)

## 1. 链接Redis <a id="&#x94FE;&#x63A5;redis"></a>

```text
package main

import (
    "fmt"
    "github.com/garyburd/redigo/redis"
)

func main() {
    c, err := redis.Dial("tcp", "localhost:6379")
    if err != nil {
        fmt.Println("conn redis failed,", err)
        return
    } 

    fmt.Println("redis conn success")

    defer c.Close()
}
```

