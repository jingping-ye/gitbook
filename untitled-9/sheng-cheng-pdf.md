# 生成PDF

## 1. 生成PDF <a id="&#x751F;&#x6210;pdf"></a>

一个简单但是非常实用的pdf生成器！

安装：

```text
    go get github.com/jung-kurt/gofpdf
```

代码：

```text
package main

import (
    "github.com/jung-kurt/gofpdf"
)

func main() {
    err := GeneratePdf("hello.pdf")
    if err != nil {
        panic(err)
    }
}



func GeneratePdf(filename string) error {

    pdf := gofpdf.New("P", "mm", "A4", "")
    pdf.AddPage()
    pdf.SetFont("Arial", "B", 16)

    
    pdf.CellFormat(190, 7, "Welcome to topgoer.com", "0", 0, "CM", false, 0, "")

    
    pdf.ImageOptions(
        "topgoer.png",
        80, 20,
        0, 0,
        false,
        gofpdf.ImageOptions{ImageType: "PNG", ReadDpi: true},
        0,
        "",
    )

    return pdf.OutputFileAndClose(filename)
}
```

有关更多信息和可用方法，请参见[库的文档](https://godoc.org/github.com/jung-kurt/gofpdf)。

