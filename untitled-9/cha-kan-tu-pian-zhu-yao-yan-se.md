# 查看图片主要颜色

## 1. 查看图片主要颜色 <a id="&#x67E5;&#x770B;&#x56FE;&#x7247;&#x4E3B;&#x8981;&#x989C;&#x8272;"></a>

这款插件是介绍如何从一张图片中快速的提取出来主要是的3种颜色。

```text
package main

import (
    "fmt"
    "image"
    "log"
    "os"

    "github.com/EdlinOrg/prominentcolor"
)

func main() {

    
    img, err := loadImage("example.jpg")
    if err != nil {
        log.Fatal("Failed to load image", err)
    }

    
    colours, err := prominentcolor.Kmeans(img)
    if err != nil {
        log.Fatal("Failed to process image", err)
    }

    fmt.Println("Dominant colours:")
    for _, colour := range colours {
        fmt.Println("#" + colour.AsString())
    }
}

func loadImage(fileInput string) (image.Image, error) {
    f, err := os.Open(fileInput)
    if err != nil {
        return nil, err
    }
    defer f.Close()
    img, _, err := image.Decode(f)
    return img, err
}
```

