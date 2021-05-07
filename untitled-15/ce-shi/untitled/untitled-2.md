# 示例测试-地鼠文档

## 源代码目录结构 <a id="bfvs5h"></a>

我们在gotest包中创建两个文件，目录结构如下所示：

```text
[GoExpert]
|
   |
      |
      |
```

其中`example.go`为源代码文件，`example_test.go`为测试文件。

## 源代码文件 <a id="5bga07"></a>

源代码文件`example.go`中包含`SayHello()`、`SayGoodbye()`和`PrintNames()`三个方法，如下所示：

```text
package gotest

import "fmt"


func SayHello() {
    fmt.Println("Hello World")
}


func SayGoodbye() {
    fmt.Println("Hello,")
    fmt.Println("goodbye")
}


func PrintNames() {
    students := make(map[int]string, 4)
    students[1] = "Jim"
    students[2] = "Bob"
    students[3] = "Tom"
    students[4] = "Sue"
    for _, value := range students {
        fmt.Println(value)
    }
}
```

这几个方法打印内容略有不同，分别代表一种典型的场景：

* SayHello\(\)：只有一行打印输出
* SayGoodbye\(\)：有两行打印输出
* PrintNames\(\)：有多行打印输出，且由于Map数据结构的原因，多行打印次序是随机的。

## 测试文件 <a id="3i2ukq"></a>

测试文件`example_test.go`中包含3个测试方法，于源代码文件中的3个方法一一对应，测试文件如下所示：

```text
package gotest_test

import "gotest"


func ExampleSayHello() {
    gotest.SayHello()
    
}


func ExampleSayGoodbye() {
    gotest.SayGoodbye()
    
    
    
}


func ExamplePrintNames() {
    gotest.PrintNames()
    
    
    
    
    
}
```

例子测试函数命名规则为”ExampleXxx”，其中”Xxx”为自定义的标识，通常为待测函数名称。

这三个测试函数分别代表三种场景：

* ExampleSayHello\(\)： 待测试函数只有一行输出，使用”// OutPut: “检测。
* ExampleSayGoodbye\(\)：待测试函数有多行输出，使用”// OutPut: “检测，其中期望值也是多行。
* ExamplePrintNames\(\)：待测试函数有多行输出，但输出次序不确定，使用”// Unordered output:”检测。

注：字符串比较时会忽略前后的空白字符。

## 执行测试 <a id="8f2kfn"></a>

命令行下，使用`go test`或`go test example_test.go`命令即可启动测试，如下所示：

```text
E:\OpenSource\GitHub\RainbowMango\GoExpertProgrammingSourceCode\GoExpert\src\gotest>go test example_test.go
ok      command-line-arguments  0.331s
```

## 总结 <a id="57e1gj"></a>

1. 例子测试函数名需要以”Example”开头；
2. 检测单行输出格式为“// Output: &lt;期望字符串&gt;”；
3. 检测多行输出格式为“// Output: \ &lt;期望字符串&gt; \ &lt;期望字符串&gt;”，每个期望字符串占一行；
4. 检测无序输出格式为”// Unordered output: \ &lt;期望字符串&gt; \ &lt;期望字符串&gt;”，每个期望字符串占一行；
5. 测试字符串时会自动忽略字符串前后的空白字符；
6. 如果测试函数中没有“Output”标识，则该测试函数不会被执行；
7. 执行测试可以使用`go test`，此时该目录下的其他测试文件也会一并执行；
8. 执行测试可以使用`go test`，此时仅执行特定文件中的测试函数；

文档更新时间: 2020-08-08 22:00   作者：kuteng

