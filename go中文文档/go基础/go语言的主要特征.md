# Go 语言的主要特征

## 1. Go 语言的主要特征

### 1.1. golang 简介

#### 1.1.1. 来历

很久以前，有一个 IT 公司（应该是 Google?），这公司有个传统，允许员工拥有 20%自由时间来开发实验性项目。在 2007 的某一天，公司的几个大牛，正在用 c++开发一些比较繁琐但是核心的工作，主要包括庞大的分布式集群，大牛觉得很闹心，后来 c++委员会来他们公司演讲，说 c++将要添加大概 35 种新特性。这几个大牛的其中一个人，名为：Rob Pike，听后心中一万个 xxx 飘过，“c++特性还不够多吗？简化 c++应该更有成就感吧”。于是乎，Rob Pike 和其他几个大牛讨论了一下，怎么解决这个问题，过了一会，Rob Pike 说要不我们自己搞个语言吧，名字叫“go”，非常简短，容易拼写。其他几位大牛就说好啊，然后他们找了块白板，在上面写下希望能有哪些功能（详见文尾）。接下来的时间里，大牛们开心的讨论设计这门语言的特性，经过漫长的岁月，他们决定，以 c 语言为原型，以及借鉴其他语言的一些特性，来解放程序员，解放自己，然后在 2009 年，go 语言诞生。

#### 1.1.2. 思想

Less can be more 大道至简,小而蕴真 让事情变得复杂很容易，让事情变得简单才难 深刻的工程文化

#### 1.1.3. 优点

- 自带 gc。

- 静态编译，编译好后，扔服务器直接运行。

- 简单的思想，没有继承，多态，类等。

- 丰富的库和详细的开发文档。

- 语法层支持并发，和拥有同步并发的 channel 类型，使并发开发变得非常方便。

- 简洁的语法，提高开发效率，同时提高代码的阅读性和可维护性。

- 超级简单的交叉编译，仅需更改环境变量。

Go 语言是谷歌 2009 年首次推出并在 2012 年正式发布的一种全新的编程语言，可以在不损失应用程序性能的情况下降低代码的复杂性。谷歌首席软件工程师罗布派克\(Rob Pike\)说：我们之所以开发 Go，是因为过去 10 多年间软件开发的难度令人沮丧。Google 对 Go 寄予厚望，其设计是让软件充分发挥多核心处理器同步多工的优点，并可解决面向对象程序设计的麻烦。它具有现代的程序语言特色，如垃圾回收，帮助开发者处理琐碎但重要的内存管理问题。Go 的速度也非常快，几乎和 C 或 C++ 程序一样快，且能够快速开发应用程序。

#### 1.1.4. Go 语言的主要特征： <a id="go&#x8BED;&#x8A00;&#x7684;&#x4E3B;&#x8981;&#x7279;&#x5F81;&#xFF1A;"></a>

```text
    1.自动立即回收。
    2.更丰富的内置类型。
    3.函数多返回值。
    4.错误处理。
    5.匿名函数和闭包。
    6.类型和接口。
    7.并发编程。
    8.反射。
    9.语言交互性。
```

#### 1.1.5. Golang 文件名： <a id="golang&#x6587;&#x4EF6;&#x540D;&#xFF1A;"></a>

```text
`所有的go源码都是以 ".go" 结尾。`
```

#### 1.1.6. Go 语言命名： <a id="go&#x8BED;&#x8A00;&#x547D;&#x540D;&#xFF1A;"></a>

1.Go 的函数、变量、常量、自定义类型、包`(package)`的命名方式遵循以下规则：

```text
    1）首字符可以是任意的Unicode字符或者下划线
    2）剩余字符可以是Unicode字符、下划线、数字
    3）字符长度不限
```

2.Go 只有 25 个关键字

```text
    break        default      func         interface    select
    case         defer        go           map          struct
    chan         else         goto         package      switch
    const        fallthrough  if           range        type
    continue     for          import       return       var
```

3.Go 还有 37 个保留字

```text
    Constants:    true  false  iota  nil

    Types:    int  int8  int16  int32  int64
              uint  uint8  uint16  uint32  uint64  uintptr
              float32  float64  complex128  complex64
              bool  byte  rune  string  error

    Functions:   make  len  cap  new  append  copy  close  delete
                 complex  real  imag
                 panic  recover
```

4.可见性：

```text
    1）声明在函数内部，是函数的本地值，类似private
    2）声明在函数外部，是对当前包可见(包内所有.go文件都可见)的全局值，类似protect
    3）声明在函数外部且首字母大写是所有包可见的全局值,类似public
```

#### 1.1.7. Go 语言声明： <a id="go&#x8BED;&#x8A00;&#x58F0;&#x660E;&#xFF1A;"></a>

有四种主要声明方式：

```text
    var（声明变量）, const（声明常量）, type（声明类型） ,func（声明函数）。
```

Go 的程序是保存在多个.go 文件中，文件的第一行就是 package XXX 声明，用来说明该文件属于哪个包\(package\)，package 声明下来就是 import 声明，再下来是类型，变量，常量，函数的声明。

#### 1.1.8. Go 项目构建及编译 <a id="go&#x9879;&#x76EE;&#x6784;&#x5EFA;&#x53CA;&#x7F16;&#x8BD1;"></a>

一个 Go 工程中主要包含以下三个目录：

```text
    src：源代码文件
    pkg：包文件
    bin：相关bin文件
```

1: 建立工程文件夹 goproject

2: 在工程文件夹中建立 src,pkg,bin 文件夹

3: 在 GOPATH 中添加 projiect 路径 例 e:/goproject

4: 在 src 文件夹下建立以包名命名的文件夹 例 examplepackage

5：在 src 文件夹下编写主程序代码代码 goproject.go

6：在 examplepackage 文件夹中编写 examplepackage.go 和 包测试文件 examplepackage_test.go

7：编译调试包

go build examplepackage

go test examplepackage

go install examplepackage

这时在 pkg 文件夹中可以发现会有一个相应的操作系统文件夹如 windows_386z, 在这个文件夹中会有 examplepackage 文件夹，在该文件中有 examplepackage.a 文件

8：编译主程序

go build goproject.go

成功后会生成 goproject.exe 文件

至此一个 Go 工程编辑成功。

以下为示例代码：

````bash
1.建立工程文件夹 go
$ pwd
/Users/***/Desktop/go
2: 在工程文件夹中建立src,pkg,bin文件夹
$ ls
bin        conf    pkg        src
3: 在GOPATH中添加projiect路径
$ go env
GOPATH="/Users/liupengjie/Desktop/go"
4: 那在src文件夹下建立以自己的包 example 文件夹
$ cd src/
$ mkdir example
5：在src文件夹下编写主程序代码代码 goproject.go
6：在example文件夹中编写 example.go 和 包测试文件 example_test.go

example.go 写入如下代码：
```go
package example

func add(a, b int) int {
    return a + b
}

func sub(a, b int) int {
    return a - b
}
```

example_test.go 写入如下代码：

```go
package example

import (
    "testing"
)

func TestAdd(t *testing.T) {
    r := add(2, 4)
    if r != 6 {
        t.Fatalf("add(2, 4) error, expect:%d, actual:%d", 6, r)
    }
    t.Logf("test add succ")
}
```

7：编译调试包
$ go build example
$ go test example
ok example 0.013s
$ go install example

$ ls /Users/**_/Desktop/go/pkg/
darwin_amd64
$ ls /Users/_**/Desktop/go/pkg/darwin_amd64/
example.a
8：编译主程序
oproject.go 写入如下代码：
package main

    import (
        "fmt"
    )

    func main(){
        fmt.Println("go project test")
    }

    $ go build goproject.go
    $ ls
    example        goproject.go    goproject

       成功后会生成goproject文件
    至此一个Go工程编辑成功。

       运行该文件：
    $ ./goproject
    go project test

````

#### 1.1.9. go 编译问题 <a id="go-&#x7F16;&#x8BD1;&#x95EE;&#x9898;"></a>

golang 的编译使用命令 go build , go install;除非仅写一个 main 函数，否则还是准备好目录结构； GOPATH=工程根目录；其下应创建 src，pkg，bin 目录，bin 目录中用于生成可执行文件，pkg 目录中用于生成.a 文件； golang 中的 import name，实际是到 GOPATH 中去寻找 name.a, 使用时是该 name.a 的源码中生命的 package 名字；这个在前面已经介绍过了。

注意点：

```text
    1.系统编译时 go install abc_name时，系统会到GOPATH的src目录中寻找abc_name目录，然后编译其下的go文件；

    2.同一个目录中所有的go文件的package声明必须相同，所以main方法要单独放一个文件，否则在eclipse和liteide中都会报错；
    编译报错如下：（假设test目录中有个main.go 和mymath.go,其中main.go声明package为main，mymath.go声明packag 为test);

        $ go install test

        can't load package: package test: found packages main (main.go) and test (mymath.go) in /home/wanjm/go/src/test

        报错说 不能加载package test（这是命令行的参数），因为发现了两个package，分别时main.go 和 mymath.go;

    3.对于main方法，只能在bin目录下运行 go build path_tomain.go; 可以用-o参数指出输出文件名；

    4.可以添加参数 go build -gcflags "-N -l" ****,可以更好的便于gdb；详细参见 http://golang.org/doc/gdb

    5.gdb全局变量主一点。 如有全局变量 a；则应写为 p 'main.a'；注意但引号不可少；
```
