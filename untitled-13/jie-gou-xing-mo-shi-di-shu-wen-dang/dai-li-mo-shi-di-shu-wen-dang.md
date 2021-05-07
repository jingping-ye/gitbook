# 代理模式-地鼠文档

代理模式用于延迟处理操作或者在进行实际操作前后进行其它处理。

### 代理模式的常见用法有 <a id="46ri2"></a>

* 虚代理
* COW代理
* 远程代理
* 保护代理
* Cache 代理
* 防火墙代理
* 同步代理
* 智能指引

等。。。

## proxy.go <a id="7igd0g"></a>

```text
package proxy

type Subject interface {
    Do() string
}

type RealSubject struct{}

func (RealSubject) Do() string {
    return "real"
}

type Proxy struct {
    real RealSubject
}

func (p Proxy) Do() string {
    var res string

    
    res += "pre:"

    
    res += p.real.Do()

    
    res += ":after"

    return res
}
```

## proxy\_test.go <a id="dj1o1u"></a>

```text
package proxy

import "testing"

func TestProxy(t *testing.T) {
    var sub Subject
    sub = &Proxy{}

    res := sub.Do()

    if res != "pre:real:after" {
        t.Fail()
    }
}
```

文档更新时间: 2020-08-24 11:18   作者：kuteng

