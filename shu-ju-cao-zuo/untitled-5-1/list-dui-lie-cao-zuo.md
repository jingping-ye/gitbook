# List队列操作

## 1. List队列操作 <a id="list&#x961F;&#x5217;&#x64CD;&#x4F5C;"></a>

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
    _, err = c.Do("lpush", "book_list", "abc", "ceg", 300)
    if err != nil {
        fmt.Println(err)
        return
    }

    r, err := redis.String(c.Do("lpop", "book_list"))
    if err != nil {
        fmt.Println("get abc failed,", err)
        return
    }

    fmt.Println(r)
}
```

运行结果：

```text
    300
```

Redis命令行：

```text
    127.0.0.1:6379> lpop book_list
    "ceg"
    127.0.0.1:6379> lpop book_list
    "abc"
    127.0.0.1:6379> lpop book_list
    (nil)
```

