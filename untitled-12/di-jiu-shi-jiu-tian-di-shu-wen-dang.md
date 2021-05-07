# 第九十九天-地鼠文档

## 1.下面代码能编译通过吗？ <a id="ao43zm"></a>

```text
func main() {
    true := false
    fmt.Println(true)
}
```

参考答案即解析：编译通过。true 是预定义标识符可以用作变量名，但是不建议这么做。

## 2.下面的代码输出什么？ <a id="ymidl"></a>

```text
func watShadowDefer(i int) (ret int) {
    ret = i * 2
    if ret > 10 {
        ret := 10
        defer func() {
            ret = ret + 1
        }()
    }
    return
}

func main() {
    result := watShadowDefer(50)
    fmt.Println(result)
}
```

参考答案即解析：100。知识点：变量作用域和defer 返回值。

文档更新时间: 2021-04-20 19:07   作者：kuteng

