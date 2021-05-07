# sha

## 1. sha <a id="sha"></a>

安全散列算法SHA（Secure Hash Algorithm）是美国国家安全局 （NSA） 设计，美国国家标准与技术研究院（NIST） 发布的一系列密码散列函数，包括 SHA-1、SHA-224、SHA-256、SHA-384 和 SHA-512 等变体。主要适用于数字签名标准（DigitalSignature Standard DSS）里面定义的数字签名算法（Digital Signature Algorithm DSA）。SHA-1已经不是那边安全了，google和微软都已经弃用这个加密算法。为此，我们使用热门的比特币使用过的算法SHA-256作为实例。其它SHA算法，也可以按照这种模式进行使用。

### 1.1.1. sha1 <a id="sha1"></a>

```text
package main

import (
    "crypto/sha1"
    "encoding/hex"
    "fmt"
    "io"
)

func main() {
    str := "www.5lmh.com"

    
    data := []byte(str)
    has := sha1.Sum(data)
    shastr1 := fmt.Sprintf("%x", has) 

    fmt.Println(shastr1)

    

    w := sha1.New()
    io.WriteString(w, str) 
    bw := w.Sum(nil)       

    
    shastr2 := hex.EncodeToString(bw) 
    fmt.Println(shastr2)
}
```

输出结果：

```text
    85f1dafe3287dce1d8ac1a72fe7f28faa2b0fbf7
    85f1dafe3287dce1d8ac1a72fe7f28faa2b0fbf7
```

哈希值用作表示大量数据的固定大小的唯一值。数据的少量更改会在哈希值中产生不可预知的大量更改。 SHA256 算法的哈希值大小为 256 位。

### 1.1.2. sha256 <a id="sha256"></a>

```text
package main

import (
    "crypto/sha256"
    "encoding/hex"
    "fmt"
    "io"
)

func main() {
    str := "www.5lmh.com"

    w := sha256.New()
    io.WriteString(w, str) 
    bw := w.Sum(nil)       

    
    shastr2 := hex.EncodeToString(bw) 
    fmt.Println(shastr2)
}
```

输出结果：

```text
    e9c2efc35f3115c82bd97ae895b96db6a483a198a8b4b1c9bd8249129db7dbe9
```

### 1.1.3. sha512 <a id="sha512"></a>

```text
package main

import (
    "crypto/sha512"
    "encoding/hex"
    "fmt"
    "io"
)

func main() {
    str := "www.5lmh.com"

    w := sha512.New()
    io.WriteString(w, str) 
    bw := w.Sum(nil)       

    
    shastr2 := hex.EncodeToString(bw) 
    fmt.Println(shastr2)
}
```

输出结果：

```text
    f4b68e0c8a85ddac35085eb95feb398361fe5c0421922c52dc7797c699664ee13aa4297dc7f20a9cd6615bf000dde6e91cc164988f7c55fc3b4c4c516b8d78c3
```

