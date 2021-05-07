# 复合主键

* [**1.** 复合主键](fu-he-zhu-jian.md#复合主键)

## 1. 复合主键 <a id="&#x590D;&#x5408;&#x4E3B;&#x952E;"></a>

可以设置多个字段为主键来开启复合主键功能：

```text
type Product struct {
    ID           string `gorm:"primary_key"`
    LanguageCode string `gorm:"primary_key"`
  Code         string
  Name         string
}
```

