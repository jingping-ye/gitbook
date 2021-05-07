# 操作ETCD

## 1. 操作ETCD <a id="&#x64CD;&#x4F5C;etcd"></a>

这里使用官方的`etcd/clientv3`包来连接etcd并进行相关操作。

### 1.1.1. 安装 <a id="&#x5B89;&#x88C5;"></a>

```text
    go get go.etcd.io/etcd/clientv3
```

### 1.1.2. put和get操作 <a id="put&#x548C;get&#x64CD;&#x4F5C;"></a>

put命令用来设置键值对数据，get命令用来根据key获取值。

```text
package main

import (
    "context"
    "fmt"
    "time"

    "go.etcd.io/etcd/clientv3"
)




func main() {
    cli, err := clientv3.New(clientv3.Config{
        Endpoints:   []string{"127.0.0.1:2379"},
        DialTimeout: 5 * time.Second,
    })
    if err != nil {
        
        fmt.Printf("connect to etcd failed, err:%v\n", err)
        return
    }
    fmt.Println("connect to etcd success")
    defer cli.Close()
    
    ctx, cancel := context.WithTimeout(context.Background(), time.Second)
    _, err = cli.Put(ctx, "lmh", "lmh")
    cancel()
    if err != nil {
        fmt.Printf("put to etcd failed, err:%v\n", err)
        return
    }
    
    ctx, cancel = context.WithTimeout(context.Background(), time.Second)
    resp, err := cli.Get(ctx, "lmh")
    cancel()
    if err != nil {
        fmt.Printf("get from etcd failed, err:%v\n", err)
        return
    }
    for _, ev := range resp.Kvs {
        fmt.Printf("%s:%s\n", ev.Key, ev.Value)
    }
}
```

### 1.1.3. watch操作 <a id="watch&#x64CD;&#x4F5C;"></a>

watch用来获取未来更改的通知。

```text
package main

import (
    "context"
    "fmt"
    "time"

    "go.etcd.io/etcd/clientv3"
)



func main() {
    cli, err := clientv3.New(clientv3.Config{
        Endpoints:   []string{"127.0.0.1:2379"},
        DialTimeout: 5 * time.Second,
    })
    if err != nil {
        fmt.Printf("connect to etcd failed, err:%v\n", err)
        return
    }
    fmt.Println("connect to etcd success")
    defer cli.Close()
    
    rch := cli.Watch(context.Background(), "lmh") 
    for wresp := range rch {
        for _, ev := range wresp.Events {
            fmt.Printf("Type: %s Key:%s Value:%s\n", ev.Type, ev.Kv.Key, ev.Kv.Value)
        }
    }
}
```

将上面的代码保存编译执行，此时程序就会等待etcd中lmh这个key的变化。

例如：我们打开终端执行以下命令修改、删除、设置lmh这个key。

```text
    etcd> etcdctl.exe --endpoints=http://127.0.0.1:2379 put lmh "lmh1"
    OK

    etcd> etcdctl.exe --endpoints=http://127.0.0.1:2379 del lmh
    1

    etcd> etcdctl.exe --endpoints=http://127.0.0.1:2379 put lmh "lmh2"
    OK
```

上面的程序都能收到如下通知。

```text
    watch>watch.exe
    connect to etcd success
    Type: PUT Key:lmh Value:lmh1
    Type: DELETE Key:lmh Value:
    Type: PUT Key:lmh Value:lmh2
```

### 1.1.4. lease租约 <a id="lease&#x79DF;&#x7EA6;"></a>

```text
package main

import (
    "fmt"
    "time"
)



import (
    "context"
    "log"

    "go.etcd.io/etcd/clientv3"
)

func main() {
    cli, err := clientv3.New(clientv3.Config{
        Endpoints:   []string{"127.0.0.1:2379"},
        DialTimeout: time.Second * 5,
    })
    if err != nil {
        log.Fatal(err)
    }
    fmt.Println("connect to etcd success.")
    defer cli.Close()

    
    resp, err := cli.Grant(context.TODO(), 5)
    if err != nil {
        log.Fatal(err)
    }

    
    _, err = cli.Put(context.TODO(), "/lmh/", "lmh", clientv3.WithLease(resp.ID))
    if err != nil {
        log.Fatal(err)
    }
}
```

### 1.1.5. keepAlive <a id="keepalive"></a>

```text
package main

import (
    "context"
    "fmt"
    "log"
    "time"

    "go.etcd.io/etcd/clientv3"
)



func main() {
    cli, err := clientv3.New(clientv3.Config{
        Endpoints:   []string{"127.0.0.1:2379"},
        DialTimeout: time.Second * 5,
    })
    if err != nil {
        log.Fatal(err)
    }
    fmt.Println("connect to etcd success.")
    defer cli.Close()

    resp, err := cli.Grant(context.TODO(), 5)
    if err != nil {
        log.Fatal(err)
    }

    _, err = cli.Put(context.TODO(), "/lmh/", "lmh", clientv3.WithLease(resp.ID))
    if err != nil {
        log.Fatal(err)
    }

    
    ch, kaerr := cli.KeepAlive(context.TODO(), resp.ID)
    if kaerr != nil {
        log.Fatal(kaerr)
    }
    for {
        ka := <-ch
        fmt.Println("ttl:", ka.TTL)
    }
}
```

### 1.1.6. 基于etcd实现分布式锁 <a id="&#x57FA;&#x4E8E;etcd&#x5B9E;&#x73B0;&#x5206;&#x5E03;&#x5F0F;&#x9501;"></a>

go.etcd.io/etcd/clientv3/concurrency在etcd之上实现并发操作，如分布式锁、屏障和选举。

导入该包：

```text
    import "go.etcd.io/etcd/clientv3/concurrency"
```

基于etcd实现的分布式锁示例：

```text
cli, err := clientv3.New(clientv3.Config{Endpoints: endpoints})
if err != nil {
    log.Fatal(err)
}
defer cli.Close()


s1, err := concurrency.NewSession(cli)
if err != nil {
    log.Fatal(err)
}
defer s1.Close()
m1 := concurrency.NewMutex(s1, "/my-lock/")

s2, err := concurrency.NewSession(cli)
if err != nil {
    log.Fatal(err)
}
defer s2.Close()
m2 := concurrency.NewMutex(s2, "/my-lock/")


if err := m1.Lock(context.TODO()); err != nil {
    log.Fatal(err)
}
fmt.Println("acquired lock for s1")

m2Locked := make(chan struct{})
go func() {
    defer close(m2Locked)
    
    if err := m2.Lock(context.TODO()); err != nil {
        log.Fatal(err)
    }
}()

if err := m1.Unlock(context.TODO()); err != nil {
    log.Fatal(err)
}
fmt.Println("released lock for s1")

<-m2Locked
fmt.Println("acquired lock for s2")
```

输出：

```text
    acquired lock for s1
    released lock for s1
    acquired lock for s2
```

[查看文档了解更多](https://godoc.org/go.etcd.io/etcd/clientv3/concurrency)

