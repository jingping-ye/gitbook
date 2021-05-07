# go操作memcached · Go语言中文文档

## 1. go操作memcached <a id="go&#x64CD;&#x4F5C;memcached"></a>

go使用memcached需要第三方的驱动库，这里有一个库是memcached作者亲自实现的，代码质量效率肯定会有保障

### 1.1.1. 安装 <a id="&#x5B89;&#x88C5;"></a>

```text
    go get github.com/bradfitz/gomemcache/memcache
```

### 1.1.2. 使用 <a id="&#x4F7F;&#x7528;"></a>

```text
    import "github.com/bradfitz/gomemcache/memcache"
```

### 1.1.3. 栗子\(吃的那种\) <a id="&#x6817;&#x5B50;&#x5403;&#x7684;&#x90A3;&#x79CD;"></a>

```text
package main

import (
    "fmt"
    "github.com/bradfitz/gomemcache/memcache"
)

var (
    server = "127.0.0.1:11211"
)

func main() {
    
    mc := memcache.New(server)
    if mc == nil {
        fmt.Println("memcache New failed")
    }

    
    mc.Set(&memcache.Item{Key: "foo", Value: []byte("my value")})

    
    it, _ := mc.Get("foo")
    if string(it.Key) == "foo" {
        fmt.Println("value is ", string(it.Value))
    } else {
        fmt.Println("Get failed")
    }
    
    mc.Add(&memcache.Item{Key: "foo", Value: []byte("bluegogo")})
    it, err := mc.Get("foo")
    if err != nil {
        fmt.Println("Add failed")
    } else {
        if string(it.Key) == "foo" {
            fmt.Println("Add value is ", string(it.Value))
        } else {
            fmt.Println("Get failed")
        }
    }
    
    mc.Replace(&memcache.Item{Key: "foo", Value: []byte("mobike")})
    it, err = mc.Get("foo")
    if err != nil {
        fmt.Println("Replace failed")
    } else {
        if string(it.Key) == "foo" {
            fmt.Println("Replace value is ", string(it.Value))
        } else {
            fmt.Println("Replace failed")
        }
    }
    
    err = mc.Delete("foo")
    if err != nil {
        fmt.Println("Delete failed:", err.Error())
    }
    
    err = mc.Set(&memcache.Item{Key: "aaa", Value: []byte("1")})
    if err != nil {
        fmt.Println("Set failed :", err.Error())
    }
    it, err = mc.Get("foo")
    if err != nil {
        fmt.Println("Get failed ", err.Error())
    } else {
        fmt.Println("src value is:", it.Value)
    }
    value, err := mc.Increment("aaa", 7)
    if err != nil {
        fmt.Println("Increment failed")
    } else {
        fmt.Println("after increment the value is :", value)
    }
    
    value, err = mc.Decrement("aaa", 4)
    if err != nil {
        fmt.Println("Decrement failed", err.Error())
    } else {
        fmt.Println("after decrement the value is ", value)
    }
}
```

