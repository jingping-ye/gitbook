# 查询结果反射结构体

## 1. 查询结果反射结构体 <a id="&#x67E5;&#x8BE2;&#x7ED3;&#x679C;&#x53CD;&#x5C04;&#x7ED3;&#x6784;&#x4F53;"></a>

本文讲解的实例是从mysql查询过来的数据如何反射到结构体字段,具体实现方法如下代码;

代码目录：

1129

-common

--common.go //是封装的代码

-main.go //是测试代码

代码的封装：

```text
package common

import (
    "errors"
    "reflect"
    "strconv"
    "time"
)


func DataToStructByTagSql(data map[string]string, obj interface{}) {
    objValue := reflect.ValueOf(obj).Elem()
    for i := 0; i < objValue.NumField(); i++ {
        
        value := data[objValue.Type().Field(i).Tag.Get("sql")]
        
        name := objValue.Type().Field(i).Name
        
        structFieldType := objValue.Field(i).Type()
        
        val := reflect.ValueOf(value)
        var err error
        if structFieldType != val.Type() {
            
            val, err = TypeConversion(value, structFieldType.Name()) 
            if err != nil {

            }
        }
        
        objValue.FieldByName(name).Set(val)
    }
}


func TypeConversion(value string, ntype string) (reflect.Value, error) {
    if ntype == "string" {
        return reflect.ValueOf(value), nil
    } else if ntype == "time.Time" {
        t, err := time.ParseInLocation("2006-01-02 15:04:05", value, time.Local)
        return reflect.ValueOf(t), err
    } else if ntype == "Time" {
        t, err := time.ParseInLocation("2006-01-02 15:04:05", value, time.Local)
        return reflect.ValueOf(t), err
    } else if ntype == "int" {
        i, err := strconv.Atoi(value)
        return reflect.ValueOf(i), err
    } else if ntype == "int8" {
        i, err := strconv.ParseInt(value, 10, 64)
        return reflect.ValueOf(int8(i)), err
    } else if ntype == "int32" {
        i, err := strconv.ParseInt(value, 10, 64)
        return reflect.ValueOf(int64(i)), err
    } else if ntype == "int64" {
        i, err := strconv.ParseInt(value, 10, 64)
        return reflect.ValueOf(i), err
    } else if ntype == "float32" {
        i, err := strconv.ParseFloat(value, 64)
        return reflect.ValueOf(float32(i)), err
    } else if ntype == "float64" {
        i, err := strconv.ParseFloat(value, 64)
        return reflect.ValueOf(i), err
    }

    

    return reflect.ValueOf(value), errors.New("未知的类型：" + ntype)
}
```

代码的测试：

```text
package main

import (
    "fmt"

    "github.com/student/1129/common"
)


type Product struct {
    ID           int64  `json:"id" sql:"id"`
    ProductClass string `json:"ProductClass" sql:"ProductClass"`
    ProductName  string `json:"ProductName" sql:"productName"`
    ProductNum   int64  `json:"ProductNum" sql:"productNum"`
    ProductImage string `json:"ProductImage" sql:"productImage"`
    ProductURL   string `json:"ProductUrl" sql:"productUrl" `
}

func main() {
    
    data := map[string]string{"id": "1", "ProductClass": "blog", "productName": "5lmh.com", "productNum": "40", "productImage": "http://www.5lmh.com/", "productUrl": "http://www.5lmh.com/"}
    productResult := &Product{}
    common.DataToStructByTagSql(data, productResult)
    fmt.Println(*productResult)
    
    Alldata := []map[string]string{
        {"id": "1", "ProductClass": "blog", "productName": "5lmh.com", "productNum": "40", "productImage": "http://www.5lmh.com/", "productUrl": "http://www.5lmh.com/"},
        {"id": "2", "ProductClass": "blog", "productName": "5lmh.com", "productNum": "40", "productImage": "http://www.5lmh.com/", "productUrl": "http://www.5lmh.com/"},
    }
    var productArray []*Product
    for _, v := range Alldata {
        Allproduct := &Product{}
        common.DataToStructByTagSql(v, Allproduct)
        productArray = append(productArray, Allproduct)
    }
    for _, vv := range productArray {
        fmt.Println(vv)
    }
}
```

