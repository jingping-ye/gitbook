# 二维码

## 1. 二维码 <a id="&#x4E8C;&#x7EF4;&#x7801;"></a>

### 1.1.1. skip2/go-qrcode生成二维码 <a id="skip2go-qrcode&#x751F;&#x6210;&#x4E8C;&#x7EF4;&#x7801;"></a>

#### 安装 <a id="&#x5B89;&#x88C5;"></a>

```text
    go get github.com/skip2/go-qrcode
```

qrcode将会内置命令行工具$GOPATH/bin/。

#### 用法 <a id="&#x7528;&#x6CD5;"></a>

```text
    import qrcode "github.com/skip2/go-qrcode"
```

* 创建一个256x256的PNG图片：

```text
    var png []byte
      png, err := qrcode.Encode("http://www.topgoer.com", qrcode.Medium, 256)
```

* 创建一个256x256的PNG图像并写入文件：

```text
    err := qrcode.WriteFile("http://www.topgoer.com", qrcode.Medium, 256, "qr.png")
```

* 创建具有自定义颜色的256x256 PNG图像并写入文件：

```text
     err := qrcode.WriteColorFile("http://www.topgoer.com", qrcode.Medium, 256, color.Black, color.White, "qr.png")
```

* 示例

```text
package main

import qrcode "github.com/skip2/go-qrcode"
import "fmt"

func main() {
    err := qrcode.WriteFile("http://www.topgoer.com", qrcode.Medium, 256, "qr.png")
    if err != nil {
        fmt.Println("write error")
    }
}
```

### 1.1.2. boombuler/barcode生成二维码 <a id="boombulerbarcode&#x751F;&#x6210;&#x4E8C;&#x7EF4;&#x7801;"></a>

* 示例

```text
package main

import (
    "image/png"
    "os"

    "github.com/boombuler/barcode"
    "github.com/boombuler/barcode/qr"
)

func main() {

    qrCode, _ := qr.Encode("http://www.topgoer.com", qr.M, qr.Auto)

    qrCode, _ = barcode.Scale(qrCode, 256, 256)

    file, _ := os.Create("qr2.png")
    defer file.Close()

    png.Encode(file, qrCode)
}
```

### 1.1.3. tuotoo/qrcode识别二维码 <a id="tuotooqrcode&#x8BC6;&#x522B;&#x4E8C;&#x7EF4;&#x7801;"></a>

* 动态二值化
* 提升图片扫描的速度:OK
* 修复标线取值: OK
* 容错码纠正数据:OK
* 数据编码方式 Numbert alphanumeric 8-bit byte: OK Kanji
* 识别各角度倾斜的二维码

#### 示例 <a id="&#x793A;&#x4F8B;"></a>

```text
package main

import (
    "fmt"
    "os"

    "github.com/tuotoo/qrcode"
)

func main() {

    fi, err := os.Open("qr2.png")
    if err != nil {
        fmt.Println(err.Error())
        return
    }
    defer fi.Close()
    qrmatrix, err := qrcode.Decode(fi)
    if err != nil {
        fmt.Println(err.Error())
        return
    }
    fmt.Println(qrmatrix.Content)
}
```

