# Hash表

* [**1.** Hash表](hash-biao.md#hash表)

## 1. Hash表 <a id="hash&#x8868;"></a>

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

    defer c.Close()
    _, err = c.Do("HSet", "books", "abc", 100)
    if err != nil {
        fmt.Println(err)
        return
    }

    r, err := redis.Int(c.Do("HGet", "books", "abc"))
    if err != nil {
        fmt.Println("get abc failed,", err)
        return
    }

    fmt.Println(r)
}
```

运行结果：

```text
    100
```

Redis命令行：

```text
    127.0.0.1:6379> hget books abc
    "100"
```

