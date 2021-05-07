# PDF转JPG

## 1. PDF转JPG <a id="pdf&#x8F6C;jpg"></a>

在下面的示例中，我们将该gographics/imagick包用作ImageMagick的C库的包装，以将我们的PDF转换为JPG。处理过程如下：我们使用软件包将测试文件加载到测试文件中，然后通过设置分辨率，压缩级别和alpha通道设置进行处理，然后保存最终的输出文件。由于该库基于C构建，因此重要的是我们必须适当调用Terminate和Destroy函数以检查内存使用情况。

在Ubuntu 18.04下运行的前提条件：

```text
    sudo apt install libmagic-dev libmagickwand-dev
```

代码：

```text
package main

import (
    "log"
    "gopkg.in/gographics/imagick.v2/imagick"
)

func main() {

    pdfName := "ref.pdf"
    imageName := "test.jpg"

    if err := ConvertPdfToJpg(pdfName, imageName); err != nil {
        log.Fatal(err)
    }
}




func ConvertPdfToJpg(pdfName string, imageName string) error {

    
    imagick.Initialize()
    defer imagick.Terminate()

    mw := imagick.NewMagickWand()
    defer mw.Destroy()

    
    
    if err := mw.SetResolution(300, 300); err != nil {
        return err
    }

    
    if err := mw.ReadImage(pdfName); err != nil {
        return err
    }

    
    
    if err := mw.SetImageAlphaChannel(imagick.ALPHA_CHANNEL_FLATTEN); err != nil {
        return err
    }

    
    if err := mw.SetCompressionQuality(95); err != nil {
        return err
    }

    
    mw.SetIteratorIndex(0)

    
    if err := mw.SetFormat("jpg"); err != nil {
        return err
    }

    
    return mw.WriteImage(imageName)
}
```

如果您看到类似以下的错误，请参阅[指南](https://alexvanderbist.com/posts/2018/fixing-imagick-error-unauthorized)。

```text
    ERROR_POLICY: not authorized `TestPdf.pdf' @ error/constitute.c/ReadImage/412
```

