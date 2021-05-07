# 第一个go程序

## 1. 第一个go程序 <a id="&#x7B2C;&#x4E00;&#x4E2A;go&#x7A0B;&#x5E8F;"></a>

### 1.1. Hello World <a id="hello-world"></a>

学习语言的第一个程序肯定是hello word了

\(1\)进入前面创建的三个目录里面的src目录

\(2\)在`src`目录下创建一个hello目录，在hello目录中创建一个`main.go`文件：

```text
package main  // 声明 main 包，表明当前是一个可执行程序

import "fmt"  // 导入内置 fmt 

func main(){  // main函数，是程序执行的入口
    fmt.Println("Hello World!")  // 在终端打印 Hello World!
}
```

\(3\)在hello目录下执行：`go build`

`go`编译器会去 `GOPATH`的`src`目录下查找你要编译的`hello`项目

编译得到的可执行文件会保存在执行编译命令的当前目录下，如果是`windows`平台会在当前目录下找到`hello.exe`可执行文件。

\(4\)在终端直接执行该`hello.exe`文件：

```text
d:\goproject\src\hello>hello.exe
Hello World!
```

我们还可以使用-o参数来指定编译后可执行文件的名字。

`go build -o heiheihei.exe`

