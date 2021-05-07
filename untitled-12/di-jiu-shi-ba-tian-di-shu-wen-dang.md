# 第九十八天-地鼠文档

## 1.下面代码输出什么？ <a id="3yrie4"></a>

```text
func main() {
    a := 1
    for i := 0;i<5;i++ {
        a := a + 1
        a = a * 2
    }
    fmt.Println(a)
}
```

参考答案及解析：1。知识点：变量的作用域。注意 for 语句的变量 a 是重新声明，它的作用范围只在 for 语句范围内。

## 2.下面的代码输出什么？ <a id="cdie2c"></a>

```text
func test(i int) (ret int) {
    ret = i * 2
    if ret > 10 {
        ret := 10
        return
    }
    return
}

func main() {
    result := test(10)
    fmt.Println(result)
}
```

参考答案即解析：编译错误。知识点：变量的作用域。编译错误信息：ret is shadowed during return。

文档更新时间: 2021-04-20 19:07   作者：kuteng

