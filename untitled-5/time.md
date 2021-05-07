# Time

## 1. Time <a id="time"></a>

时间和日期是我们编程中经常会用到的，本文主要介绍了Go语言内置的time包的基本用法。

### 1.1.1. time包 <a id="time&#x5305;"></a>

time包提供了时间的显示和测量用的函数。日历的计算采用的是公历。

### 1.1.2. 时间类型 <a id="&#x65F6;&#x95F4;&#x7C7B;&#x578B;"></a>

time.Time类型表示时间。我们可以通过time.Now\(\)函数获取当前的时间对象，然后获取时间对象的年月日时分秒等信息。示例代码如下：

```text
func timeDemo() {
    now := time.Now() 
    fmt.Printf("current time:%v\n", now)

    year := now.Year()     
    month := now.Month()   
    day := now.Day()       
    hour := now.Hour()     
    minute := now.Minute() 
    second := now.Second() 
    fmt.Printf("%d-%02d-%02d %02d:%02d:%02d\n", year, month, day, hour, minute, second)
}
```

### 1.1.3. 时间戳 <a id="&#x65F6;&#x95F4;&#x6233;"></a>

时间戳是自1970年1月1日（08:00:00GMT）至当前时间的总毫秒数。它也被称为Unix时间戳（UnixTimestamp）。

基于时间对象获取时间戳的示例代码如下：

```text
func timestampDemo() {
    now := time.Now()            
    timestamp1 := now.Unix()     
    timestamp2 := now.UnixNano() 
    fmt.Printf("current timestamp1:%v\n", timestamp1)
    fmt.Printf("current timestamp2:%v\n", timestamp2)
}
```

使用time.Unix\(\)函数可以将时间戳转为时间格式。

```text
func timestampDemo2(timestamp int64) {
    timeObj := time.Unix(timestamp, 0) 
    fmt.Println(timeObj)
    year := timeObj.Year()     
    month := timeObj.Month()   
    day := timeObj.Day()       
    hour := timeObj.Hour()     
    minute := timeObj.Minute() 
    second := timeObj.Second() 
    fmt.Printf("%d-%02d-%02d %02d:%02d:%02d\n", year, month, day, hour, minute, second)
}
```

### 1.1.4. 时间间隔 <a id="&#x65F6;&#x95F4;&#x95F4;&#x9694;"></a>

time.Duration是time包定义的一个类型，它代表两个时间点之间经过的时间，以纳秒为单位。time.Duration表示一段时间间隔，可表示的最长时间段大约290年。

time包中定义的时间间隔类型的常量如下：

```text
const (
    Nanosecond  Duration = 1
    Microsecond          = 1000 * Nanosecond
    Millisecond          = 1000 * Microsecond
    Second               = 1000 * Millisecond
    Minute               = 60 * Second
    Hour                 = 60 * Minute
)
```

例如：time.Duration表示1纳秒，time.Second表示1秒。

### 1.1.5. 时间操作 <a id="&#x65F6;&#x95F4;&#x64CD;&#x4F5C;"></a>

#### Add <a id="add"></a>

我们在日常的编码过程中可能会遇到要求时间+时间间隔的需求，Go语言的时间对象有提供Add方法如下：

```text
    func (t Time) Add(d Duration) Time
```

举个例子，求一个小时之后的时间：

```text
func main() {
    now := time.Now()
    later := now.Add(time.Hour) 
    fmt.Println(later)
}
```

#### Sub <a id="sub"></a>

求两个时间之间的差值：

```text
    func (t Time) Sub(u Time) Duration
```

返回一个时间段t-u。如果结果超出了Duration可以表示的最大值/最小值，将返回最大值/最小值。要获取时间点t-d（d为Duration），可以使用t.Add\(-d\)。

#### Equal <a id="equal"></a>

```text
    func (t Time) Equal(u Time) bool
```

判断两个时间是否相同，会考虑时区的影响，因此不同时区标准的时间也可以正确比较。本方法和用t==u不同，这种方法还会比较地点和时区信息。

#### Before <a id="before"></a>

```text
    func (t Time) Before(u Time) bool
```

如果t代表的时间点在u之前，返回真；否则返回假。

#### After <a id="after"></a>

```text
    func (t Time) After(u Time) bool
```

如果t代表的时间点在u之后，返回真；否则返回假。

### 1.1.6. 定时器 <a id="&#x5B9A;&#x65F6;&#x5668;"></a>

使用time.Tick\(时间间隔\)来设置定时器，定时器的本质上是一个通道（channel）。

```text
func tickDemo() {
    ticker := time.Tick(time.Second) 
    for i := range ticker {
        fmt.Println(i)
    }
}
```

### 1.1.7. 时间格式化 <a id="&#x65F6;&#x95F4;&#x683C;&#x5F0F;&#x5316;"></a>

时间类型有一个自带的方法Format进行格式化，需要注意的是Go语言中格式化时间模板不是常见的Y-m-d H:M:S而是使用Go的诞生时间2006年1月2号15点04分（记忆口诀为2006 1 2 3 4）。也许这就是技术人员的浪漫吧。

补充：如果想格式化为12小时方式，需指定PM。

```text
func formatDemo() {
    now := time.Now()
    
    
    fmt.Println(now.Format("2006-01-02 15:04:05.000 Mon Jan"))
    
    fmt.Println(now.Format("2006-01-02 03:04:05.000 PM Mon Jan"))
    fmt.Println(now.Format("2006/01/02 15:04"))
    fmt.Println(now.Format("15:04 2006/01/02"))
    fmt.Println(now.Format("2006/01/02"))
}
```

#### 解析字符串格式的时间 <a id="&#x89E3;&#x6790;&#x5B57;&#x7B26;&#x4E32;&#x683C;&#x5F0F;&#x7684;&#x65F6;&#x95F4;"></a>

```text
now := time.Now()
fmt.Println(now)

loc, err := time.LoadLocation("Asia/Shanghai")
if err != nil {
    fmt.Println(err)
    return
}

timeObj, err := time.ParseInLocation("2006/01/02 15:04:05", "2019/08/04 14:15:20", loc)
if err != nil {
    fmt.Println(err)
    return
}
fmt.Println(timeObj)
fmt.Println(timeObj.Sub(now))
```

