# 缓存

## 1. 缓存 <a id="&#x7F13;&#x5B58;"></a>

go-cache是​​内存中的键：类似于memcached的值存储/缓存，适用于在一台计算机上运行的应用程序。它的主要优点是，由于本质上是map\[string\]interface{}具有到期时间的线程安全的，因此不需要通过网络序列化或传输其内容。

可以在给定的持续时间内或永久存储任何对象，并且可以由多个goroutine安全使用缓存。

尽管不打算将go-cache用作持久性数据存储，但可以将整个缓存保存到文件中并从文件中加载（c.Items\(\)用于检索要映射的项目映射并NewFrom\(\)从反序列化的缓存中创建缓存）以进行恢复从停机时间很快。（有关NewFrom\(\)警告，请参阅文档。）

### 1.1.1. 安装 <a id="&#x5B89;&#x88C5;"></a>

```text
import (
    "fmt"
    "github.com/patrickmn/go-cache"
    "time"
)

func main() {
    
    
    c := cache.New(5*time.Minute, 10*time.Minute)

    
    c.Set("foo", "bar", cache.DefaultExpiration)

    
    
    
    c.Set("baz", 42, cache.NoExpiration)

    
    foo, found := c.Get("foo")
    if found {
        fmt.Println(foo)
    }

    
    
    
    
    
    foo, found := c.Get("foo")
    if found {
        MyFunction(foo.(string))
    }

    
    
    if x, found := c.Get("foo"); found {
        foo := x.(string)
        
    }
    
    var foo string
    if x, found := c.Get("foo"); found {
        foo = x.(string)
    }
    
    

    
    c.Set("foo", &MyStruct, cache.DefaultExpiration)
    if x, found := c.Get("foo"); found {
        foo := x.(*MyStruct)
            
    }
}
```

