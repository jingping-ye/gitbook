# cron

## 1. cron <a id="cron"></a>

### 1.1.1. cron 表达式的基本格式 <a id="cron-&#x8868;&#x8FBE;&#x5F0F;&#x7684;&#x57FA;&#x672C;&#x683C;&#x5F0F;"></a>

用过 linux 的应该对 cron 有所了解。linux 中可以通过 crontab -e 来配置定时任务。不过，linux 中的 cron 只能精确到分钟。而我们这里要讨论的 Go 实现的 cron 可以精确到秒，除了这点比较大的区别外，cron 表达式的基本语法是类似的。（如果使用过 Java 中的 Quartz，对 cron 表达式应该比较了解，而且它和这里我们将要讨论的 Go 版 cron 很像，也都精确到秒） cron\(计划任务\)，顾名思义，按照约定的时间，定时的执行特定的任务（job）。cron 表达式 表达了这种约定。 cron 表达式代表了一个时间集合，使用 6 个空格分隔的字段表示。

| 字段名 | 是否必须 | 允许的值 | 允许的特定字符 |
| :--- | :--- | :--- | :--- |
| 秒\(Seconds\) | 是 | 0-59 | \* / , - |
| 分\(Minutes\) | 是 | 0-59 | \* / , - |
| 时\(Hours\) | 是 | 0-23 | \* / , - |
| 日\(Day of month\) | 是 | 1-31 | \* / , – ? |
| 月\(Month\) | 是 | 1-12 or JAN-DEC | \* / , - |
| 星期\(Day of week\) | 否 | 0-6 or SUM-SAT | \* / , – ? |

注：

1）月\(Month\)和星期\(Day of week\)字段的值不区分大小写，如：SUN、Sun 和 sun 是一样的。 2）星期 \(Day of week\)字段如果没提供，相当于是 \*

### 1.1.2. 特殊字符说明 <a id="&#x7279;&#x6B8A;&#x5B57;&#x7B26;&#x8BF4;&#x660E;"></a>

1）星号`(*)` 表示 cron 表达式能匹配该字段的所有值。如在第5个字段使用星号\(month\)，表示每个月

2）斜线\(/\) 表示增长间隔，如第1个字段\(minutes\) 值是 3-59/15，表示每小时的第3分钟开始执行一次，之后每隔 15 分钟执行一次（即 3、18、33、48 这些时间点执行），这里也可以表示为：3/15

3）逗号\(,\) 用于枚举值，如第6个字段值是 MON,WED,FRI，表示 星期一、三、五 执行

4）连字号\(-\) 表示一个范围，如第3个字段的值为 9-17 表示 9am 到 5pm 直接每个小时（包括9和17）

5）问号\(?\) 只用于日\(Day of month\)和星期\(Day of week\)，\表示不指定值，可以用于代替 \*

### 1.1.3. cron举例说明 <a id="cron&#x4E3E;&#x4F8B;&#x8BF4;&#x660E;"></a>

* 每隔5秒执行一次：`*/5 * * * * ?`
* 每隔1分钟执行一次：`0 */1 * * * ?`
* 每天23点执行一次：`0 0 23 * * ?`
* 每天凌晨1点执行一次：`0 0 1 * * ?`
* 每月1号凌晨1点执行一次：`0 0 1 1 * ?`
* 在26分、29分、33分执行一次：`0 26,29,33 * * * ?`
* 每天的0点、13点、18点、21点都执行一次：`0 0 0,13,18,21 * * ?`

### 1.1.4. 示例 <a id="&#x793A;&#x4F8B;"></a>

最简单crontab任务

根据官网手册写法

```text
package main

import (
    "github.com/robfig/cron"
    "log"
)

func main() {
    i := 0
    c := cron.New()
    spec := "*/5 * * * * ?"
    _,err:=c.AddFunc(spec, func() {
        i++
        log.Println("cron running:", i)
    })
    fmt.Println(err)
    c.Start()

    select{}
}
```

会报一个错误

```text
    expected exactly 5 fields, found 6: [*/5 * * * * ?]
```

这就尴尬了，查询大量资料，把代码修改为一下代码就可以完美运行了

```text
package main

import (
    "fmt"

    "github.com/robfig/cron"
)


func newWithSeconds() *cron.Cron {
    secondParser := cron.NewParser(cron.Second | cron.Minute |
        cron.Hour | cron.Dom | cron.Month | cron.DowOptional | cron.Descriptor)
    return cron.New(cron.WithParser(secondParser), cron.WithChain())
}
func main() {
    i := 0
    c := newWithSeconds()
    spec := "0 */1 * * * ?"  
    c.AddFunc(spec, func() {
        i++
        fmt.Println("cron running:", i)
    })
    c.Start()

    select {}
}
```

输出结果：

```text
    cron running: 1
    cron running: 2
    cron running: 3
```

多个定时crontab任务

```text
package main

import (
    "fmt"
    "log"

    "github.com/robfig/cron"
)

type TestJob struct {
}

func (this TestJob) Run() {
    fmt.Println("testJob1...")
}

type Test2Job struct {
}

func (this Test2Job) Run() {
    fmt.Println("testJob2...")
}


func newWithSeconds() *cron.Cron {
    secondParser := cron.NewParser(cron.Second | cron.Minute |
        cron.Hour | cron.Dom | cron.Month | cron.DowOptional | cron.Descriptor)
    return cron.New(cron.WithParser(secondParser), cron.WithChain())
}
func main() {
    i := 0
    c := newWithSeconds()
    
    spec := "*/5 * * * * ?"
    c.AddFunc(spec, func() {
        i++
        log.Println("cron running:", i)
    })

    
    c.AddJob(spec, TestJob{})
    c.AddJob(spec, Test2Job{})

    
    c.Start()

    
    defer c.Stop()

    select {}
}
```

输出结果：

```text
    2019/11/14 17:23:10 cron running: 1
    testJob2...
    testJob1...
    2019/11/14 17:23:15 cron running: 2
    testJob2...
    testJob1...
```

