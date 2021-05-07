# hmac

* [**1.** hmac](hmac.md#hmac)

## 1. hmac <a id="hmac"></a>

HMAC是密钥相关的哈希运算消息认证码，HMAC运算利用哈希算法，以一个密钥和一个消息为输入，生成一个消息摘要作为输出。

主要用于验证接口签名~

md5 、hmac、sha1算法的简单实现:

```text
package main

import (
    "crypto/hmac"
    "crypto/md5"
    "encoding/hex"
    "fmt"
)

func main() {
    key := "kuteng"
    data := "www.5lmh.com"
    hmac := hmac.New(md5.New, []byte(key))
    hmac.Write([]byte(data))
    fmt.Println(hex.EncodeToString(hmac.Sum([]byte(""))))
}
```

输出结果：

```text
    679f5d6f7d344dba1e33938ae1d41ab4
```

