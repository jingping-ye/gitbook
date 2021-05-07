# WaitGroup-地鼠文档

## 1 前言 <a id="5asb84"></a>

WaitGroup是Golang应用开发过程中经常使用的并发控制技术。

WaitGroup，可理解为Wait-Goroutine-Group，即等待一组goroutine结束。比如某个goroutine需要等待其他几个goroutine全部完成，那么使用WaitGroup可以轻松实现。

下面程序展示了一个goroutine等待另外两个goroutine结束的例子：

```text
package main

import (
    "fmt"
    "time"
    "sync"
)

func main() {
    var wg sync.WaitGroup

    wg.Add(2) 
    go func() {
        
        time.Sleep(1*time.Second)

        fmt.Println("Goroutine 1 finished!")
        wg.Done() 
    }()

    go func() {
        
        time.Sleep(2*time.Second)

        fmt.Println("Goroutine 2 finished!")
        wg.Done() 
    }()

    wg.Wait() 
    fmt.Printf("All Goroutine finished!")
}
```

简单的说，上面程序中wg内部维护了一个计数器：

1. 启动goroutine前将计数器通过Add\(2\)将计数器设置为待启动的goroutine个数。
2. 启动goroutine后，使用Wait\(\)方法阻塞自己，等待计数器变为0。
3. 每个goroutine执行结束通过Done\(\)方法将计数器减1。
4. 计数器变为0后，阻塞的goroutine被唤醒。

其实WaitGroup也可以实现一组goroutine等待另一组goroutine，这有点像玩杂技，很容出错，如果不了解其实现原理更是如此。实际上，WaitGroup的实现源码非常简单。

## 2 基础知识 <a id="t9cos"></a>

### 2.1 信号量 <a id="8081cs"></a>

信号量是Unix系统提供的一种保护共享资源的机制，用于防止多个线程同时访问某个资源。

可简单理解为信号量为一个数值：

* 当信号量&gt;0时，表示资源可用，获取信号量时系统自动将信号量减1；
* 当信号量==0时，表示资源暂不可用，获取信号量时，当前线程会进入睡眠，当信号量为正时被唤醒；

由于WaitGroup实现中也使用了信号量，在此做个简单介绍。

## 3 WaitGroup <a id="c62v5a"></a>

### 3.1 数据结构 <a id="9k4cab"></a>

源码包中`src/sync/waitgroup.go:WaitGroup`定义了其数据结构：

```text
type WaitGroup struct {
    state1 [3]uint32
}
```

state1是个长度为3的数组，其中包含了state和一个信号量，而state实际上是两个计数器：

* counter： 当前还未执行结束的goroutine计数器
* waiter count: 等待goroutine-group结束的goroutine数量，即有多少个等候者
* semaphore: 信号量

考虑到字节是否对齐，三者出现的位置不同，为简单起见，依照字节已对齐情况下，三者在内存中的位置如下所示：

WaitGroup对外提供三个接口：

* Add\(delta int\): 将delta值加到counter中
* Wait\(\)： waiter递增1，并阻塞等待信号量semaphore
* Done\(\)： counter递减1，按照waiter数值释放相应次数信号量

下面分别介绍这三个函数的实现细节。

### 3.2 Add\(delta int\) <a id="dznpla"></a>

Add\(\)做了两件事，一是把delta值累加到counter中，因为delta可以为负值，也就是说counter有可能变成0或负值，所以第二件事就是当counter值变为0时，根据waiter数值释放等量的信号量，把等待的goroutine全部唤醒，如果counter变为负值，则panic.

Add\(\)伪代码如下：

```text
func (wg *WaitGroup) Add(delta int) {
    statep, semap := wg.state() 

    state := atomic.AddUint64(statep, uint64(delta)<<32) 
    v := int32(state >> 32) 
    w := uint32(state)      

    if v < 0 {              
        panic("sync: negative WaitGroup counter")
    }

    
    
    
    if v > 0 || w == 0 {
        return
    }

    
    
    *statep = 0
    for ; w != 0; w-- {
        runtime_Semrelease(semap, false) 
    }
}
```

### 3.3 Wait\(\) <a id="8tdpdk"></a>

Wait\(\)方法也做了两件事，一是累加waiter, 二是阻塞等待信号量

```text
func (wg *WaitGroup) Wait() {
    statep, semap := wg.state() //获取state和semaphore地址指针
    for {
        state := atomic.LoadUint64(statep) //获取state值
        v := int32(state >> 32)            //获取counter值
        w := uint32(state)                 //获取waiter值
        if v == 0 {                        //如果counter值为0，说明所有goroutine都退出了，不需要待待，直接返回
            return
        }

        // 使用CAS（比较交换算法）累加waiter，累加可能会失败，失败后通过for loop下次重试
        if atomic.CompareAndSwapUint64(statep, state, state+1) {
            runtime_Semacquire(semap) //累加成功后，等待信号量唤醒自己
            return
        }
    }
}
```

这里用到了CAS算法保证有多个goroutine同时执行Wait\(\)时也能正确累加waiter。

### 3.4 Done\(\) <a id="3lhqiw"></a>

Done\(\)只做一件事，即把counter减1，我们知道Add\(\)可以接受负值，所以Done实际上只是调用了Add\(-1\)。

源码如下：

```text
func (wg *WaitGroup) Done() {
    wg.Add(-1)
}
```

Done\(\)的执行逻辑就转到了Add\(\)，实际上也正是最后一个完成的goroutine把等待者唤醒的。

### 4 小结 <a id="1912sy"></a>

简单说来，`WaitGroup`通常用于等待一组“工作协程”结束的场景，其内部维护两个计数器，这里把它们称为“工作协程”计数器和“坐等协程”计数器，  
`WaitGroup`对外提供的三个方法分工非常明确：

* `Add(delta int)`方法用于增加“工作协程”计数，通常在启动新的“工作协程”之前调用；
* `Done()`方法用于减少“工作协程”计数，每次调用递减`1`，通常在“工作协程”内部且在临近返回之前调用；
* `Wait()`方法用于增加“坐等协程”计数，通常在所有”工作协程”全部启动之后调用；

`Done()`方法除了负责递减“工作协程”计数以外，还会在“工作协程”计数变为`0`时检查“坐等协程”计数器并把“坐等协程”唤醒。  
需要注意的是，`Done()`方法递减“工作协程”计数后，如果“工作协程”计数变成负数时，将会触发`panic`，这就要求`Add()`方法调用要早于`Done()`方法。

此外，通过`Add()`方法累加的“工作协程”计数要与实际需要等待的“工作协程”数量一致，否则也会触发`panic`。  
当“工作协程”计数多于实际需要等待的“工作协程”数量时，“坐等协程”可能会永远无法被唤醒而产生列锁，此时，Go运行时检测到死锁会触发`panic`,  
当“工作协程”计数小于实际需要等待的“工作协程”数量时，`Done()`会在“工作协程”计数变为负数时触发`panic`。

文档更新时间: 2020-08-08 21:59   作者：kuteng

