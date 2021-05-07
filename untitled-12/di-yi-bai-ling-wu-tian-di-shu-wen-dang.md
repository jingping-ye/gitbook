# 第一百零五天-地鼠文档

## 1.下面代码输出什么？请简要说明。 <a id="6mu60x"></a>

```text
var c = make(chan int)
var a int

func f() {
    a = 1
    <-c
}
func main() {
    go f()
    c <- 0
    print(a)
}
```

* A. 不能编译；
* B. 输出 1；
* C. 输出 0；
* D. panic；

参考答案及解析：B。能正确输出，不过主协程会阻塞 f\(\) 函数的执行。

## 2.下面代码输出什么？请简要说明。 <a id="1qgv60"></a>

```text
type MyMutex struct {
    count int
    sync.Mutex
}

func main() {
    var mu MyMutex
    mu.Lock()
    var mu1 = mu
    mu.count++
    mu.Unlock()
    mu1.Lock()
    mu1.count++
    mu1.Unlock()
    fmt.Println(mu.count, mu1.count)
}
```

* A. 不能编译；
* B. 输出 1, 1；
* C. 输出 1, 2；
* D. fatal error；

参考答案及解析：D。加锁后复制变量，会将锁的状态也复制，所以 mu1 其实是已经加锁状态，再加锁会死锁。

文档更新时间: 2021-04-20 19:07   作者：kuteng

