# 零碎知识点

* [**1.** 零碎知识点](ling-sui-zhi-shi-dian.md#零碎知识点)

## 1. 零碎知识点 <a id="&#x96F6;&#x788E;&#x77E5;&#x8BC6;&#x70B9;"></a>

### 1.new\(\) 与 make\(\) 的区别 <a id="1new-&#x4E0E;-make-&#x7684;&#x533A;&#x522B;"></a>

new\(T\) 和 make\(T,args\) 是 Go 语言内建函数，用来分配内存，但适用的类型不同。

new\(T\) 会为 T 类型的新值分配已置零的内存空间，并返回地址（指针），即类型为 `*T` 的值。换句话说就是，返回一个指针，该指针指向新分配的、类型为 T 的零值。适用于值类型，如数组、结构体等。

make\(T,args\) 返回初始化之后的 T 类型的值，这个值并不是 T 类型的零值，也不是指针 `*T`，是经过初始化之后的 T 的引用。make\(\) 只适用于 slice、map 和 channel.

### 2.`defer func() { recover() }()` 可以拦截panic错误 <a id="2defer-func--recover--&#x53EF;&#x4EE5;&#x62E6;&#x622A;panic&#x9519;&#x8BEF;"></a>

### 3.转换小写字母 <a id="3&#x8F6C;&#x6362;&#x5C0F;&#x5199;&#x5B57;&#x6BCD;"></a>

```text
//转换成小写字母
func toLowerCase(str string) string {
    rune_arr := []rune(str)
    for i, _ := range rune_arr {
        if rune_arr[i] >= 65 && rune_arr[i] <= 90 {
            rune_arr[i] += 32
        }
    }
    return string(rune_arr)
}
```

### 4.Golang 解决 golang.org/x/ 下包下载不下来的问题 <a id="4golang-&#x89E3;&#x51B3;-golangorgx-&#x4E0B;&#x5305;&#x4E0B;&#x8F7D;&#x4E0D;&#x4E0B;&#x6765;&#x7684;&#x95EE;&#x9898;"></a>

由于众所周知的原因，golang在下载golang.org的包时会出现访问不了的情况。尤其是x包，很多库都依赖于它。由于x包在github上都有镜像，我们可以使用从github.com上把代码clone到创建的golang。org/x目录上就OK了

* git clone [https://github.com/golang/sys.git](https://github.com/golang/sys.git)
* git clone [https://github.com/golang/net.git](https://github.com/golang/net.git)
* git clone [https://github.com/golang/text.git](https://github.com/golang/text.git)
* git clone [https://github.com/golang/lint.git](https://github.com/golang/lint.git)
* git clone [https://github.com/golang/tools.git](https://github.com/golang/tools.git)
* git clone [https://github.com/golang/crypto.git](https://github.com/golang/crypto.git)

还有其他的包可以直接进github包里面查找

