# 享元模式-地鼠文档

享元模式从对象中剥离出不发生改变且多个实例需要的重复数据，独立出一个享元，使多个对象共享，从而节省内存以及减少对象数量。

## flyweight.go <a id="ia2fv"></a>

```text
package flyweight

import "fmt"

type ImageFlyweightFactory struct {
    maps map[string]*ImageFlyweight
}

var imageFactory *ImageFlyweightFactory

func GetImageFlyweightFactory() *ImageFlyweightFactory {
    if imageFactory == nil {
        imageFactory = &ImageFlyweightFactory{
            maps: make(map[string]*ImageFlyweight),
        }
    }
    return imageFactory
}

func (f *ImageFlyweightFactory) Get(filename string) *ImageFlyweight {
    image := f.maps[filename]
    if image == nil {
        image = NewImageFlyweight(filename)
        f.maps[filename] = image
    }

    return image
}

type ImageFlyweight struct {
    data string
}

func NewImageFlyweight(filename string) *ImageFlyweight {
    
    data := fmt.Sprintf("image data %s", filename)
    return &ImageFlyweight{
        data: data,
    }
}

func (i *ImageFlyweight) Data() string {
    return i.data
}

type ImageViewer struct {
    *ImageFlyweight
}

func NewImageViewer(filename string) *ImageViewer {
    image := GetImageFlyweightFactory().Get(filename)
    return &ImageViewer{
        ImageFlyweight: image,
    }
}

func (i *ImageViewer) Display() {
    fmt.Printf("Display: %s\n", i.Data())
}
```

## flyweight\_test.go <a id="5xo7zx"></a>

```text
package flyweight

import "testing"

func ExampleFlyweight() {
    viewer := NewImageViewer("image1.png")
    viewer.Display()
    
    
}

func TestFlyweight(t *testing.T) {
    viewer1 := NewImageViewer("image1.png")
    viewer2 := NewImageViewer("image1.png")

    if viewer1.ImageFlyweight != viewer2.ImageFlyweight {
        t.Fail()
    }
}
```

文档更新时间: 2020-08-24 11:33   作者：kuteng

