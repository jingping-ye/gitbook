# form数据绑定结构体

## 1. form数据绑定结构体 <a id="form&#x6570;&#x636E;&#x7ED1;&#x5B9A;&#x7ED3;&#x6784;&#x4F53;"></a>

本实例讲解的是form表单提交的数据绑定结构体,具体实现方法如下代码;

代码目录：

1129

-common

--common.go //是封装的代码

-main.go //是测试代码

代码的封装：

```text

package common

import (
    "encoding"
    "encoding/json"
    "fmt"
    "net/url"
    "reflect"
    "strconv"
    "time"
)

const tagName = "kuteng"


type pathMap struct {
    ma    reflect.Value
    key   string
    value reflect.Value

    path string
}


type pathMaps []*pathMap


func (m pathMaps) find(id reflect.Value, key string) *pathMap {
    for i := range m {
        if m[i].ma == id && m[i].key == key {
            return m[i]
        }
    }
    return nil
}

type Error struct {
    err error
}

func (s *Error) Error() string {
    return "kuteng: " + s.err.Error()
}

func (s Error) MarshalJSON() ([]byte, error) {
    return json.Marshal(s.err.Error())
}


func (s *Error) Cause() error {
    return s.err
}

func newError(err error) *Error { return &Error{err} }


type DecodeCustomTypeFunc func([]string) (interface{}, error)


type DecodeCustomTypeField struct {
    field reflect.Value
    fun   DecodeCustomTypeFunc
}


type DecodeCustomType struct {
    fun    DecodeCustomTypeFunc
    fields []*DecodeCustomTypeField
}


type Decoder struct {
    main       reflect.Value
    formValues url.Values
    opts       *DecoderOptions

    curr   reflect.Value
    values []string

    path    string
    field   string
    bracket string
    

    maps pathMaps

    customTypes map[reflect.Type]*DecodeCustomType
}


type DecoderOptions struct {
    
    TagName string
    
    PrefUnmarshalText bool
    
    IgnoreUnknownKeys bool
}


func (dec *Decoder) RegisterCustomType(fn DecodeCustomTypeFunc, types []interface{}, fields []interface{}) *Decoder {
    if dec.customTypes == nil {
        dec.customTypes = make(map[reflect.Type]*DecodeCustomType, 100)
    }
    lenFields := len(fields)
    for i := range types {
        typ := reflect.TypeOf(types[i])
        if dec.customTypes[typ] == nil {
            dec.customTypes[typ] = &DecodeCustomType{fun: fn, fields: make([]*DecodeCustomTypeField, 0, lenFields)}
        }
        if lenFields > 0 {
            for j := range fields {
                val := reflect.ValueOf(fields[j])
                field := &DecodeCustomTypeField{field: val, fun: fn}
                dec.customTypes[typ].fields = append(dec.customTypes[typ].fields, field)
            }
        }
    }
    return dec
}


func NewDecoder(opts *DecoderOptions) *Decoder {
    dec := &Decoder{opts: opts}
    if dec.opts == nil {
        dec.opts = &DecoderOptions{}
    }
    if dec.opts.TagName == "" {
        dec.opts.TagName = tagName
    }
    return dec
}


func (dec Decoder) Decode(vs url.Values, dst interface{}) error {
    main := reflect.ValueOf(dst)
    if main.Kind() != reflect.Ptr {
        return newError(fmt.Errorf("the value passed for decode is not a pointer but a %v", main.Kind()))
    }
    dec.main = main.Elem()
    dec.formValues = vs
    return dec.init()
}


func Decode(vs url.Values, dst interface{}) error {
    main := reflect.ValueOf(dst)
    if main.Kind() != reflect.Ptr {
        return newError(fmt.Errorf("the value passed for decode is not a pointer but a %v", main.Kind()))
    }
    dec := &Decoder{
        main:       main.Elem(),
        formValues: vs,
        opts: &DecoderOptions{
            TagName: tagName,
        },
    }
    return dec.init()
}


func (dec Decoder) init() error {
    
    for k, v := range dec.formValues {
        dec.path = k
        dec.values = v
        dec.curr = dec.main
        if err := dec.analyzePath(); err != nil {
            if dec.curr.Kind() == reflect.Struct && dec.opts.IgnoreUnknownKeys {
                continue
            }
            return err
        }
    }
    
    for _, v := range dec.maps {
        key := v.ma.Type().Key()
        ptr := false
        
        var val reflect.Value
        if key.Kind() == reflect.Ptr {
            ptr = true
            val = reflect.New(key.Elem())
        } else {
            val = reflect.New(key).Elem()
        }
        
        dec.path = v.path
        dec.field = v.path
        dec.values = []string{v.key}
        dec.curr = val
        
        if err := dec.decode(); err != nil {
            return err
        }
        
        if ptr && dec.curr.Kind() != reflect.Ptr {
            dec.curr = dec.curr.Addr()
        }
        
        v.ma.SetMapIndex(dec.curr, v.value)
    }
    return nil
}


func (dec *Decoder) analyzePath() (err error) {
    inBracket := false
    bracketClosed := false
    lastPos := 0
    endPos := 0

    
    for i, char := range []byte(dec.path) {
        if char == '[' && inBracket == false {
            
            bracketClosed = false
            inBracket = true
            dec.field = dec.path[lastPos:i]
            lastPos = i + 1
            continue
        } else if inBracket {
            
            if char == ']' {
                
                
                inBracket = false
                bracketClosed = true
                dec.bracket = dec.path[lastPos:endPos]
                lastPos = i + 1
                if err = dec.traverse(); err != nil {
                    return
                }
            } else {
                
                endPos = i + 1
            }
            continue
        } else if !inBracket {
            
            if char == '.' {
                
                
                if bracketClosed {
                    bracketClosed = false
                    lastPos = i + 1
                    continue
                }
                
                dec.field = dec.path[lastPos:i]
                
                lastPos = i + 1
                if err = dec.traverse(); err != nil {
                    return
                }
            }
            continue
        }
    }
    
    dec.field = dec.path[lastPos:]

    return dec.end()
}


func (dec *Decoder) traverse() error {
    
    if dec.field != "" {
        
        switch dec.curr.Kind() {
        case reflect.Struct:
            if err := dec.findStructField(); err != nil {
                return err
            }
        case reflect.Map:
            dec.traverseInMap(true)
        }
        dec.field = ""
    }
    
    
    if dec.curr.Kind() == reflect.Interface && !dec.curr.IsNil() {
        dec.curr = dec.curr.Elem()
    }
    
    if dec.curr.Kind() == reflect.Ptr {
        if dec.curr.IsNil() {
            dec.curr.Set(reflect.New(dec.curr.Type().Elem()))
        }
        dec.curr = dec.curr.Elem()
    }
    
    if dec.bracket != "" {
        switch dec.curr.Kind() {
        case reflect.Array:
            index, err := strconv.Atoi(dec.bracket)
            if err != nil {
                return newError(fmt.Errorf("the index of array is not a number in the field \"%v\" of path \"%v\"", dec.field, dec.path))
            }
            dec.curr = dec.curr.Index(index)
        case reflect.Slice:
            index, err := strconv.Atoi(dec.bracket)
            if err != nil {
                return newError(fmt.Errorf("the index of slice is not a number in the field \"%v\" of path \"%v\"", dec.field, dec.path))
            }
            if dec.curr.Len() <= index {
                dec.expandSlice(index + 1)
            }
            dec.curr = dec.curr.Index(index)
        case reflect.Map:
            dec.traverseInMap(false)
        default:
            return newError(fmt.Errorf("the field \"%v\" in path \"%v\" has a index for array but it is a %v", dec.field, dec.path, dec.curr.Kind()))
        }
        dec.bracket = ""
    }
    return nil
}


func (dec *Decoder) traverseInMap(byField bool) {
    n := dec.curr.Type()
    makeAndAppend := func() {
        if dec.maps == nil {
            dec.maps = make(pathMaps, 0, 500)
        }
        m := reflect.New(n.Elem()).Elem()
        if byField {
            dec.maps = append(dec.maps, &pathMap{dec.curr, dec.field, m, dec.path})
        } else {
            dec.maps = append(dec.maps, &pathMap{dec.curr, dec.bracket, m, dec.path})
        }
        dec.curr = m
    }
    if dec.curr.IsNil() {
        
        dec.curr.Set(reflect.MakeMap(n))
        makeAndAppend()
    } else {
        
        var a *pathMap
        if byField {
            a = dec.maps.find(dec.curr, dec.field)
        } else {
            a = dec.maps.find(dec.curr, dec.bracket)
        }
        if a == nil {
            
            makeAndAppend()
        } else {
            dec.curr = a.value
        }
    }
}


func (dec *Decoder) end() error {
    switch dec.curr.Kind() {
    case reflect.Struct:
        if err := dec.findStructField(); err != nil {
            return err
        }
    case reflect.Map:
        
        dec.traverseInMap(true)
    }
    return dec.decode()
}


func (dec *Decoder) decode() error {
    
    if dec.opts.PrefUnmarshalText {
        if ok, err := dec.isUnmarshalText(dec.curr); ok || err != nil {
            return err
        }
        if ok, err := dec.isCustomType(); ok || err != nil {
            return err
        }
    } else {
        if ok, err := dec.isCustomType(); ok || err != nil {
            return err
        }
        if ok, err := dec.isUnmarshalText(dec.curr); ok || err != nil {
            return err
        }
    }

    switch dec.curr.Kind() {
    case reflect.Array:
        if dec.bracket == "" {
            
            if err := dec.setValues(); err != nil {
                return err
            }
        } else {
            
            index, err := strconv.Atoi(dec.bracket)
            if err != nil {
                return newError(fmt.Errorf("the index of array is not a number in the field \"%v\" of path \"%v\"", dec.field, dec.path))
            }
            dec.curr = dec.curr.Index(index)
            return dec.decode()
        }
    case reflect.Slice:
        if dec.bracket == "" {
            
            
            dec.expandSlice(len(dec.values))
            if err := dec.setValues(); err != nil {
                return err
            }
        } else {
            
            index, err := strconv.Atoi(dec.bracket)
            if err != nil {
                return newError(fmt.Errorf("the index of slice is not a number in the field \"%v\" of path \"%v\"", dec.field, dec.path))
            }
            
            if dec.curr.Len() <= index {
                dec.expandSlice(index + 1)
            }
            dec.curr = dec.curr.Index(index)
            return dec.decode()
        }
    case reflect.String:
        dec.curr.SetString(dec.values[0])
    case reflect.Int, reflect.Int8, reflect.Int16, reflect.Int32, reflect.Int64:
        if num, err := strconv.ParseInt(dec.values[0], 10, 64); err != nil {
            return newError(fmt.Errorf("the value of field \"%v\" in path \"%v\" should be a valid signed integer number", dec.field, dec.path))
        } else {
            dec.curr.SetInt(num)
        }
    case reflect.Uint, reflect.Uint8, reflect.Uint16, reflect.Uint32, reflect.Uint64, reflect.Uintptr:
        if num, err := strconv.ParseUint(dec.values[0], 10, 64); err != nil {
            return newError(fmt.Errorf("the value of field \"%v\" in path \"%v\" should be a valid unsigned integer number", dec.field, dec.path))
        } else {
            dec.curr.SetUint(num)
        }
    case reflect.Float32, reflect.Float64:
        if num, err := strconv.ParseFloat(dec.values[0], dec.curr.Type().Bits()); err != nil {
            return newError(fmt.Errorf("the value of field \"%v\" in path \"%v\" should be a valid float number", dec.field, dec.path))
        } else {
            dec.curr.SetFloat(num)
        }
    case reflect.Bool:
        switch dec.values[0] {
        case "true", "on", "1", "checked":
            dec.curr.SetBool(true)
        default:
            dec.curr.SetBool(false)
            return nil
        }
    case reflect.Interface:
        dec.curr.Set(reflect.ValueOf(dec.values[0]))
    case reflect.Ptr:
        n := reflect.New(dec.curr.Type().Elem())
        if dec.curr.CanSet() {
            dec.curr.Set(n)
        } else {
            dec.curr.Elem().Set(n.Elem())
        }
        dec.curr = dec.curr.Elem()
        return dec.decode()
    case reflect.Struct:
        switch dec.curr.Interface().(type) {
        case time.Time:
            var t time.Time
            
            if dec.values[0] != "" {
                var err error
                t, err = time.Parse("2006-01-02", dec.values[0])
                if err != nil {
                    return newError(fmt.Errorf("the value of field \"%v\" in path \"%v\" is not a valid datetime", dec.field, dec.path))
                }
            }
            dec.curr.Set(reflect.ValueOf(t))
        case url.URL:
            u, err := url.Parse(dec.values[0])
            if err != nil {
                return newError(fmt.Errorf("the value of field \"%v\" in path \"%v\" is not a valid url", dec.field, dec.path))
            }
            dec.curr.Set(reflect.ValueOf(*u))
        default:
            if dec.opts.IgnoreUnknownKeys {
                return nil
            }
            num := dec.curr.NumField()
            for i := 0; i < num; i++ {
                field := dec.curr.Type().Field(i)
                tag := field.Tag.Get(dec.opts.TagName)
                if tag == "-" {
                    
                    return nil
                }
            }
            return newError(fmt.Errorf("not supported type for field \"%v\" in path \"%v\". Maybe you should to include it the UnmarshalText interface or register it using custom type?", dec.field, dec.path))
        }
    default:
        if dec.opts.IgnoreUnknownKeys {
            return nil
        }

        return newError(fmt.Errorf("not supported type for field \"%v\" in path \"%v\"", dec.field, dec.path))
    }

    return nil
}



func (dec *Decoder) findStructField() error {
    var anon reflect.Value

    num := dec.curr.NumField()
    for i := 0; i < num; i++ {
        field := dec.curr.Type().Field(i)

        if field.Name == dec.field {
            tag := field.Tag.Get(dec.opts.TagName)
            if tag == "-" {
                
                return nil
            }
            
            dec.curr = dec.curr.Field(i)
            return nil
        } else if field.Anonymous {
            
            tmp := dec.curr
            dec.curr = dec.curr.FieldByIndex(field.Index)
            if dec.curr.Kind() == reflect.Ptr {
                if dec.curr.IsNil() {
                    dec.curr.Set(reflect.New(dec.curr.Type().Elem()))
                }
                dec.curr = dec.curr.Elem()
            }
            if err := dec.findStructField(); err != nil {
                dec.curr = tmp
                continue
            }
            
            
            
            anon = dec.curr
            dec.curr = tmp
        } else if dec.field == field.Tag.Get(dec.opts.TagName) {
            
            dec.curr = dec.curr.Field(i)
            return nil
        }
    }
    if anon.IsValid() {
        dec.curr = anon
        return nil
    }

    if dec.opts.IgnoreUnknownKeys {
        return nil
    }
    return newError(fmt.Errorf("not found the field \"%v\" in the path \"%v\"", dec.field, dec.path))
}


func (dec *Decoder) expandSlice(length int) {
    n := reflect.MakeSlice(dec.curr.Type(), length, length)
    reflect.Copy(n, dec.curr)
    dec.curr.Set(n)
}


func (dec *Decoder) setValues() error {
    tmp := dec.curr 
    for i, v := range dec.values {
        dec.curr = tmp.Index(i)
        dec.values[0] = v
        if err := dec.decode(); err != nil {
            return err
        }
    }
    return nil
}


func (dec *Decoder) isCustomType() (bool, error) {
    if dec.customTypes == nil {
        return false, nil
    }
    if v, ok := dec.customTypes[dec.curr.Type()]; ok {
        if len(v.fields) > 0 {
            for i := range v.fields {
                
                
                if v.fields[i].field.Elem() == dec.curr {
                    va, err := v.fields[i].fun(dec.values)
                    if err != nil {
                        return true, err
                    }
                    dec.curr.Set(reflect.ValueOf(va))
                    return true, nil
                }
            }
        }
        
        if v.fun != nil {
            va, err := v.fun(dec.values)
            if err != nil {
                return true, err
            }
            dec.curr.Set(reflect.ValueOf(va))
            return true, nil
        }
    }
    return false, nil
}

var (
    typeTime    = reflect.TypeOf(time.Time{})
    typeTimePtr = reflect.TypeOf(&time.Time{})
)



func (dec *Decoder) isUnmarshalText(v reflect.Value) (bool, error) {
    
    m, ok := v.Interface().(encoding.TextUnmarshaler)
    addr := v.CanAddr()
    if !ok && !addr {
        return false, nil
    } else if addr {
        return dec.isUnmarshalText(v.Addr())
    }
    
    n := v.Type()
    if n.ConvertibleTo(typeTime) || n.ConvertibleTo(typeTimePtr) {
        return false, nil
    }
    
    return true, m.UnmarshalText([]byte(dec.values[0]))
}
```

代码的测试：

```text
package main

import (
    "encoding/json"
    "fmt"

    "github.com/student/1129/common"
)


type Product struct {
    ID           int64  `json:"id" sql:"id" kuteng:"id"`
    ProductClass string `json:"ProductClass" sql:"ProductClass" kuteng:"ProductClass"`
    ProductName  string `json:"ProductName" sql:"productName" kuteng:"productName"`
    ProductNum   int64  `json:"ProductNum" sql:"productNum" kuteng:"productNum"`
    ProductImage string `json:"ProductImage" sql:"productImage" kuteng:"productImage"`
    ProductURL   string `json:"ProductUrl" sql:"productUrl"  kuteng:"productUrl"`
}

func main() {
    product :=&Product{}
    
    p.Ctx.Request().ParseForm()
    dec := common.NewDecoder(&common.DecoderOptions{TagName:"kuteng"})
    if err:= dec.Decode(p.Ctx.Request().Form,product);err!=nil {
        fmt.Println("绑定失败")
    }
}
```

