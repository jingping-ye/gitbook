# 选项设计模式

## 1. 选项设计模式 <a id="&#x9009;&#x9879;&#x8BBE;&#x8BA1;&#x6A21;&#x5F0F;"></a>

有时候一个函数会有很多参数，为了方便函数的使用，我们会给希望给一些参数设定默认值，调用时只需要传与默认值不同的参数即可，类似于 python 里面的默认参数和字典参数，虽然 golang 里面既没有默认参数也没有字典参数，但是我们有选项模式

举个例子：

首先我们定义一个结构体，初始化这个结构体，然后给结构体赋值。如果开发后期需要对结构体内的参数进行增加或者删除操作，也就需要对应的对初始化的结构体进行修改。

```text
package main

import "fmt"





type Options struct {
   str1 string
   str2 string
   int1 int
   int2 int
   int3 int
}

func InitOptions(str1 string, str2 string, int1 int, int2 int) {
   options := Options{}
   options.str1 = str1
   options.str2 = str2
   options.int1 = int1
   options.int2 = int2
   fmt.Printf("options:%#v\n", options)
}

func main() {
   InitOptions("a","b",1,2)
}
```

如果采用选项设计模式那么我们在添加或者删除一个参数的时候，只需要添加或者删除一个函数即可。

```text
package main

import "fmt"





type Options struct {
    str1 string
    str2 string
    int1 int
    int2 int
}


type Option func(opts *Options)

func InitOptions(opts ...Option) {
    options := &Options{}
    for _, opt := range opts {
        opt(options)
    }
    fmt.Printf("options:%#v\n", options)
}

func WithStringOption1(str string) Option {
    return func(opts *Options) {
        opts.str1 = str
    }
}

func WithStringOption2(str string) Option {
    return func(opts *Options) {
        opts.str2 = str
    }
}
func WithStringOption3(int1 int) Option {
    return func(opts *Options) {
        opts.int1 = int1
    }
}
func WithStringOption4(int1 int) Option {
    return func(opts *Options) {
        opts.int2 = int1
    }
}
func main() {
    InitOptions(WithStringOption1("5lmh.com"), WithStringOption2("topgoer.com"), WithStringOption3(5), WithStringOption4(6))
}
```

**选项模式的应用**

从这里可以看到，为了实现选项的功能，我们增加了很多的代码，实现成本相对还是较高的，所以实践中需要根据自己的业务场景去权衡是否需要使用。个人总结满足下面条件可以考虑使用选项模式

* 参数确实比较复杂，影响调用方使用
* 参数确实有比较清晰明确的默认值
* 为参数的后续拓展考虑

在 golang 的很多开源项目里面也用到了选项模式，比如 grpc 中的 rpc 方法就是采用选项模式设计的，除了必填的 rpc 参数外，还可以一些选项参数，grpc\_retry 就是通过这个机制实现的，可以实现自动重试功能。

