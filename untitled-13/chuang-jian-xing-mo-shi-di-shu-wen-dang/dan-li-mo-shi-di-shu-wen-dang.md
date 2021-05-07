# 单例模式-地鼠文档

使用懒惰模式的单例模式，使用双重检查加锁保证线程安全

## singleton.go <a id="15z3hx"></a>

```text
package singleton

import "sync"


type Singleton struct{}

var singleton *Singleton
var once sync.Once


func GetInstance() *Singleton {
    once.Do(func() {
        singleton = &Singleton{}
    })

    return singleton
}
```

## singleton\_test.go <a id="8kn5rk"></a>

```text
package singleton

import (
    "sync"
    "testing"
)

const parCount = 100

func TestSingleton(t *testing.T) {
    ins1 := GetInstance()
    ins2 := GetInstance()
    if ins1 != ins2 {
        t.Fatal("instance is not equal")
    }
}

func TestParallelSingleton(t *testing.T) {
    wg := sync.WaitGroup{}
    wg.Add(parCount)
    instances := [parCount]*Singleton{}
    for i := 0; i < parCount; i++ {
        go func(index int) {
            instances[index] = GetInstance()
            wg.Done()
        }(i)
    }
    wg.Wait()
    for i := 1; i < parCount; i++ {
        if instances[i] != instances[i-1] {
            t.Fatal("instance is not equal")
        }
    }
}
```

文档更新时间: 2020-08-24 11:08   作者：kuteng

