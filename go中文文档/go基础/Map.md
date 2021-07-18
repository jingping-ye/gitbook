# Map

## 1. Map <a id="map"></a>

map是一种无序的基于key-value的数据结构，Go语言中的map是引用类型，必须初始化才能使用。

### 1.1.1. map定义 <a id="map&#x5B9A;&#x4E49;"></a>

Go语言中 map的定义语法如下

```text
    map[KeyType]ValueType
```

其中，

```text
    KeyType:表示键的类型。

    ValueType:表示键对应的值的类型。
```

map类型的变量默认初始值为nil，需要使用make\(\)函数来分配内存。语法为：

```text
    make(map[KeyType]ValueType, [cap])
```

其中cap表示map的容量，该参数虽然不是必须的，但是我们应该在初始化map的时候就为其指定一个合适的容量。

### 1.1.2. map基本使用 <a id="map&#x57FA;&#x672C;&#x4F7F;&#x7528;"></a>

map中的数据都是成对出现的，map的基本使用示例代码如下：

```text
func main() {
    scoreMap := make(map[string]int, 8)
    scoreMap["张三"] = 90
    scoreMap["小明"] = 100
    fmt.Println(scoreMap)
    fmt.Println(scoreMap["小明"])
    fmt.Printf("type of a:%T\n", scoreMap)
}
```

输出：

```text
    map[小明:100 张三:90]
    100
    type of a:map[string]int
```

map也支持在声明的时候填充元素，例如：

```text
func main() {
    userInfo := map[string]string{
        "username": "pprof.cn",
        "password": "123456",
    }
    fmt.Println(userInfo) 
}
```

### 1.1.3. 判断某个键是否存在 <a id="&#x5224;&#x65AD;&#x67D0;&#x4E2A;&#x952E;&#x662F;&#x5426;&#x5B58;&#x5728;"></a>

Go语言中有个判断map中键是否存在的特殊写法，格式如下:

```text
    value, ok := map[key]
```

举个例子：

```text
func main() {
    scoreMap := make(map[string]int)
    scoreMap["张三"] = 90
    scoreMap["小明"] = 100
    
    v, ok := scoreMap["张三"]
    if ok {
        fmt.Println(v)
    } else {
        fmt.Println("查无此人")
    }
}
```

### 1.1.4. map的遍历 <a id="map&#x7684;&#x904D;&#x5386;"></a>

Go语言中使用for range遍历map。

```text
func main() {
    scoreMap := make(map[string]int)
    scoreMap["张三"] = 90
    scoreMap["小明"] = 100
    scoreMap["王五"] = 60
    for k, v := range scoreMap {
        fmt.Println(k, v)
    }
}
```

但我们只想遍历key的时候，可以按下面的写法：

```text
func main() {
    scoreMap := make(map[string]int)
    scoreMap["张三"] = 90
    scoreMap["小明"] = 100
    scoreMap["王五"] = 60
    for k := range scoreMap {
        fmt.Println(k)
    }
}
```

注意： 遍历map时的元素顺序与添加键值对的顺序无关。

### 1.1.5. 使用delete\(\)函数删除键值对 <a id="&#x4F7F;&#x7528;delete&#x51FD;&#x6570;&#x5220;&#x9664;&#x952E;&#x503C;&#x5BF9;"></a>

使用delete\(\)内建函数从map中删除一组键值对，delete\(\)函数的格式如下：

```text
    delete(map, key)
```

其中，

```text
    map:表示要删除键值对的map

    key:表示要删除的键值对的键
```

示例代码如下：

```text
func main(){
    scoreMap := make(map[string]int)
    scoreMap["张三"] = 90
    scoreMap["小明"] = 100
    scoreMap["王五"] = 60
    delete(scoreMap, "小明")
    for k,v := range scoreMap{
        fmt.Println(k, v)
    }
}
```

### 1.1.6. 按照指定顺序遍历map <a id="&#x6309;&#x7167;&#x6307;&#x5B9A;&#x987A;&#x5E8F;&#x904D;&#x5386;map"></a>

```text
 func main() {
    rand.Seed(time.Now().UnixNano()) 

    var scoreMap = make(map[string]int, 200)

    for i := 0; i < 100; i++ {
        key := fmt.Sprintf("stu%02d", i) 
        value := rand.Intn(100)          
        scoreMap[key] = value
    }
    
    var keys = make([]string, 0, 200)
    for key := range scoreMap {
        keys = append(keys, key)
    }
    
    sort.Strings(keys)
    
    for _, key := range keys {
        fmt.Println(key, scoreMap[key])
    }
}
```

### 1.1.7. 元素为map类型的切片 <a id="&#x5143;&#x7D20;&#x4E3A;map&#x7C7B;&#x578B;&#x7684;&#x5207;&#x7247;"></a>

下面的代码演示了切片中的元素为map类型时的操作：

```text
func main() {
    var mapSlice = make([]map[string]string, 3)
    for index, value := range mapSlice {
        fmt.Printf("index:%d value:%v\n", index, value)
    }
    fmt.Println("after init")
    
    mapSlice[0] = make(map[string]string, 10)
    mapSlice[0]["name"] = "王五"
    mapSlice[0]["password"] = "123456"
    mapSlice[0]["address"] = "红旗大街"
    for index, value := range mapSlice {
        fmt.Printf("index:%d value:%v\n", index, value)
    }
}
```

### 1.1.8. 值为切片类型的map <a id="&#x503C;&#x4E3A;&#x5207;&#x7247;&#x7C7B;&#x578B;&#x7684;map"></a>

下面的代码演示了map中值为切片类型的操作：

```text
func main() {
    var sliceMap = make(map[string][]string, 3)
    fmt.Println(sliceMap)
    fmt.Println("after init")
    key := "中国"
    value, ok := sliceMap[key]
    if !ok {
        value = make([]string, 0, 2)
    }
    value = append(value, "北京", "上海")
    sliceMap[key] = value
    fmt.Println(sliceMap)
}
```

