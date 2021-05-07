# 基本操作测试

## 1. 基本操作测试 <a id="&#x57FA;&#x672C;&#x64CD;&#x4F5C;&#x6D4B;&#x8BD5;"></a>

简单的例子来测试下基本的操作：

```text
package main


import (
    "fmt"
    "time"

    zk "github.com/samuel/go-zookeeper/zk"
)


func getConnect(zkList []string) (conn *zk.Conn) {
    conn, _, err := zk.Connect(zkList, 10*time.Second)
    if err != nil {
        fmt.Println(err)
    }
    return
}


func test1() {
    zkList := []string{"localhost:2181"}
    conn := getConnect(zkList)

    defer conn.Close()
    var flags int32 = 0
    
    
    
    
    
    conn.Create("/go_servers", nil, flags, zk.WorldACL(zk.PermAll)) 

    time.Sleep(20 * time.Second)
}




func test2() {
    zkList := []string{"localhost:2181"}
    conn := getConnect(zkList)

    defer conn.Close()
    conn.Create("/testadaadsasdsaw", nil, zk.FlagEphemeral, zk.WorldACL(zk.PermAll))

    time.Sleep(20 * time.Second)
}


func test3() {
    zkList := []string{"localhost:2181"}
    conn := getConnect(zkList)

    defer conn.Close()

    children, _, err := conn.Children("/go_servers")
    if err != nil {
        fmt.Println(err)
    }
    fmt.Printf("%v \n", children)
}

func main() {
    test3()
}
```

