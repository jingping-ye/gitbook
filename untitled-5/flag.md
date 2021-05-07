# Flag

## 1. Flag <a id="flag"></a>

Go语言内置的flag包实现了命令行参数的解析，flag包使得开发命令行工具更为简单。

### 1.1.1. os.Args <a id="osargs"></a>

如果你只是简单的想要获取命令行参数，可以像下面的代码示例一样使用os.Args来获取命令行参数。

```text
package main

import (
    "fmt"
    "os"
)


func main() {
    
    if len(os.Args) > 0 {
        for index, arg := range os.Args {
            fmt.Printf("args[%d]=%v\n", index, arg)
        }
    }
}
```

将上面的代码执行go build -o "args\_demo"编译之后，执行：

```text
    $ ./args_demo a b c d
    args[0]=./args_demo
    args[1]=a
    args[2]=b
    args[3]=c
    args[4]=d
```

os.Args是一个存储命令行参数的字符串切片，它的第一个元素是执行文件的名称。

### 1.1.2. flag包基本使用 <a id="flag&#x5305;&#x57FA;&#x672C;&#x4F7F;&#x7528;"></a>

本文介绍了flag包的常用函数和基本用法，更详细的内容请查看[官方文档](https://studygolang.com/pkgdoc)。

#### 导入flag包 <a id="&#x5BFC;&#x5165;flag&#x5305;"></a>

```text
    import flag
```

#### flag参数类型 <a id="flag&#x53C2;&#x6570;&#x7C7B;&#x578B;"></a>

flag包支持的命令行参数类型有bool、int、int64、uint、uint64、float float64、string、duration。

| flag参数 | 有效值 |
| :--- | :--- |
| 字符串flag | 合法字符串 |
| 整数flag | 1234、0664、0x1234等类型，也可以是负数。 |
| 浮点数flag | 合法浮点数 |
| bool类型flag | 1, 0, t, f, T, F, true, false, TRUE, FALSE, True, False。 |
| 时间段flag | 任何合法的时间段字符串。如”300ms”、”-1.5h”、”2h45m”。 合法的单位有”ns”、”us” /“µs”、”ms”、”s”、”m”、”h”。 |

### 1.1.3. 定义命令行flag参数 <a id="&#x5B9A;&#x4E49;&#x547D;&#x4EE4;&#x884C;flag&#x53C2;&#x6570;"></a>

有以下两种常用的定义命令行flag参数的方法。

#### flag.Type\(\) <a id="flagtype"></a>

基本格式如下：

`flag.Type(flag名, 默认值, 帮助信息)*Type`例如我们要定义姓名、年龄、婚否三个命令行参数，我们可以按如下方式定义：

```text
name := flag.String("name", "张三", "姓名")
age := flag.Int("age", 18, "年龄")
married := flag.Bool("married", false, "婚否")
delay := flag.Duration("d", 0, "时间间隔")
```

需要注意的是，此时name、age、married、delay均为对应类型的指针。

#### flag.TypeVar\(\) <a id="flagtypevar"></a>

基本格式如下： flag.TypeVar\(Type指针, flag名, 默认值, 帮助信息\) 例如我们要定义姓名、年龄、婚否三个命令行参数，我们可以按如下方式定义：

```text
var name string
var age int
var married bool
var delay time.Duration
flag.StringVar(&name, "name", "张三", "姓名")
flag.IntVar(&age, "age", 18, "年龄")
flag.BoolVar(&married, "married", false, "婚否")
flag.DurationVar(&delay, "d", 0, "时间间隔")
```

#### flag.Parse\(\) <a id="flagparse"></a>

通过以上两种方法定义好命令行flag参数后，需要通过调用flag.Parse\(\)来对命令行参数进行解析。

支持的命令行参数格式有以下几种：

* -flag xxx （使用空格，一个-符号）
* --flag xxx （使用空格，两个-符号）
* -flag=xxx （使用等号，一个-符号）
* --flag=xxx （使用等号，两个-符号）

其中，布尔类型的参数必须使用等号的方式指定。

Flag解析在第一个非flag参数（单个”-“不是flag参数）之前停止，或者在终止符”–“之后停止。

### 1.1.4. flag其他函数 <a id="flag&#x5176;&#x4ED6;&#x51FD;&#x6570;"></a>

* flag.Args\(\) ////返回命令行参数后的其他参数，以\[\]string类型
* flag.NArg\(\) //返回命令行参数后的其他参数个数
* flag.NFlag\(\) //返回使用的命令行参数个数

### 1.1.5. 完整示例 <a id="&#x5B8C;&#x6574;&#x793A;&#x4F8B;"></a>

#### 定义 <a id="&#x5B9A;&#x4E49;"></a>

```text
func main() {
    
    var name string
    var age int
    var married bool
    var delay time.Duration
    flag.StringVar(&name, "name", "张三", "姓名")
    flag.IntVar(&age, "age", 18, "年龄")
    flag.BoolVar(&married, "married", false, "婚否")
    flag.DurationVar(&delay, "d", 0, "延迟的时间间隔")

    
    flag.Parse()
    fmt.Println(name, age, married, delay)
    
    fmt.Println(flag.Args())
    
    fmt.Println(flag.NArg())
    
    fmt.Println(flag.NFlag())
}
```

### 1.1.6. 使用 <a id="&#x4F7F;&#x7528;"></a>

命令行参数使用提示：

```text
    $ ./flag_demo -help
    Usage of ./flag_demo:
      -age int
            年龄 (default 18)
      -d duration
            时间间隔
      -married
            婚否
      -name string
            姓名 (default "张三")
```

正常使用命令行flag参数：

```text
    $ ./flag_demo -name pprof --age 28 -married=false -d=1h30m
    pprof 28 false 1h30m0s
    []
    0
    4
```

使用非flag命令行参数：

```text
    $ ./flag_demo a b c
    张三 18 false 0s
    [a b c]
    3
    0
```

