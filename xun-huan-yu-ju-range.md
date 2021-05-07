# 循环语句range

## 1. 循环语句range <a id="&#x5FAA;&#x73AF;&#x8BED;&#x53E5;range"></a>

Golang range类似迭代器操作，返回 \(索引, 值\) 或 \(键, 值\)。

for 循环的 range 格式可以对 slice、map、数组、字符串等进行迭代循环。格式如下：

```text
for key, value := range oldMap {
    newMap[key] = value
}
```

|  | 1st value | 2nd value |  |
| :--- | :--- | :--- | :--- |
| string | index | s\[index\] | unicode, rune |
| array/slice | index | s\[index\] |  |
| map | key | m\[key\] |  |
| channel | element |  |  |

可忽略不想要的返回值，或 `"_"` 这个特殊变量。

```text
package main

func main() {
    s := "abc"
    
    for i := range s {
        println(s[i])
    }
    
    for _, c := range s {
        println(c)
    }
    
    for range s {

    }

    m := map[string]int{"a": 1, "b": 2}
    
    for k, v := range m {
        println(k, v)
    }
}
```

输出结果：

```text
    97
    98
    99
    97
    98
    99
    a 1
    b 2
```

`*`注意，range 会复制对象。

```text
package main

import "fmt"

func main() {
    a := [3]int{0, 1, 2}

    for i, v := range a { 

        if i == 0 { 
            a[1], a[2] = 999, 999
            fmt.Println(a) 
        }

        a[i] = v + 100 

    }

    fmt.Println(a) 
}
```

输出结果：

```text
    [0 999 999]
    [100 101 102]
```

建议改用引用类型，其底层数据不会被复制。

```text
package main

func main() {
    s := []int{1, 2, 3, 4, 5}

    for i, v := range s { 

        if i == 0 {
            s = s[:3]  
            s[2] = 100 
        }

        println(i, v)
    }
}
```

输出结果:

```text
    0 1
    1 2
    2 100
    3 4
    4 5
```

另外两种引用类型 map、channel 是指针包装，而不像 slice 是 struct。

for 和 for range有什么区别?

主要是使用场景不同

for可以

遍历array和slice

遍历key为整型递增的map

遍历string

for range可以完成所有for可以做的事情，却能做到for不能做的，包括

遍历key为string类型的map并同时获取key和value

遍历channel

