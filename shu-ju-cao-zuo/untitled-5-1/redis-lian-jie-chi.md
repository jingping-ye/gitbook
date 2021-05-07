# Redis连接池

## 1. Redis连接池 <a id="redis&#x8FDE;&#x63A5;&#x6C60;"></a>

```text
package main
import(
    "fmt"
    "github.com/garyburd/redigo/redis"
)

var pool *redis.Pool  

func init(){
    pool = &redis.Pool{     
        MaxIdle:16,    
        
        MaxActive:0,    
        IdleTimeout:300,    
        Dial: func() (redis.Conn ,error){     
            return redis.Dial("tcp","localhost:6379")
        },
    }
}

func main(){
    c := pool.Get() 
    defer c.Close() 

        _,err := c.Do("Set","abc",200)
        if err != nil {
            fmt.Println(err)
            return
        }

        r,err := redis.Int(c.Do("Get","abc"))
        if err != nil {
            fmt.Println("get abc faild :",err)
            return
        }
        fmt.Println(r)
        pool.Close() 
}
```

运行结果：

```text
    200
```

Redis命令行：

```text
    127.0.0.1:6379> get abc
    "200"
```

