# 模型定义

## 1. 模型定义 <a id="&#x6A21;&#x578B;&#x5B9A;&#x4E49;"></a>

### 1.1. 模型定义 <a id="&#x6A21;&#x578B;&#x5B9A;&#x4E49;_1"></a>

模型一般都是普通的 Golang 的结构体，Go的基本数据类型，或者指针。[`sql.Scanner`](https://golang.org/pkg/database/sql/#Scanner) 和 [`driver.Valuer`](https://golang.org/pkg/database/sql/driver/#Valuer)，同时也支持接口。

例子：

```text
type User struct {
  gorm.Model
  Name         string
  Age          sql.NullInt64
  Birthday     *time.Time
  Email        string  `gorm:"type:varchar(100);unique_index"`
  Role         string  `gorm:"size:255"` //设置字段的大小为255个字节
  MemberNumber *string `gorm:"unique;not null"` // 设置 memberNumber 字段唯一且不为空
  Num          int     `gorm:"AUTO_INCREMENT"` // 设置 Num字段自增
  Address      string  `gorm:"index:addr"` // 给Address 创建一个名字是  `addr`的索引
  IgnoreMe     int     `gorm:"-"` //忽略这个字段
}
```

### 1.2. 结构标签 <a id="&#x7ED3;&#x6784;&#x6807;&#x7B7E;"></a>

标签是声明模型时可选的标记。GORM 支持以下标记：

#### 1.2.1. 支持的结构标签 <a id="&#x652F;&#x6301;&#x7684;&#x7ED3;&#x6784;&#x6807;&#x7B7E;"></a>

| 标签 | 说明 |
| :--- | :--- |
| Column | 指定列的名称 |
| Type | 指定列的类型 |
| Size | 指定列的大小，默认是 255 |
| PRIMARY\_KEY | 指定一个列作为主键 |
| UNIQUE | 指定一个唯一的列 |
| DEFAULT | 指定一个列的默认值 |
| PRECISION | 指定列的数据的精度 |
| NOT NULL | 指定列的数据不为空 |
| AUTO\_INCREMENT | 指定一个列的数据是否自增 |
| INDEX | 创建带或不带名称的索引，同名创建复合索引 |
| UNIQUE\_INDEX | 类似 `索引`，创建一个唯一的索引 |
| EMBEDDED | 将 struct 设置为 embedded |
| EMBEDDED\_PREFIX | 设置嵌入式结构的前缀名称 |
| - | 忽略这些字段 |

#### 1.2.2. 关联的结构标签 <a id="&#x5173;&#x8054;&#x7684;&#x7ED3;&#x6784;&#x6807;&#x7B7E;"></a>

有关详细信息，请查看「关联」部分

| 标签 | 说明 |
| :--- | :--- |
| MANY2MANY | 指定连接表名称 |
| FOREIGNKEY | 指定外键 |
| ASSOCIATION\_FOREIGNKEY | 指定关联外键 |
| POLYMORPHIC | 指定多态类型 |
| POLYMORPHIC\_VALUE | 指定多态的值 |
| JOINTABLE\_FOREIGNKEY | 指定连接表的外键 |
| ASSOCIATION\_JOINTABLE\_FOREIGNKEY | 指定连接表的关联外键 |
| SAVE\_ASSOCIATIONS | 是否自动保存关联 |
| ASSOCIATION\_AUTOUPDATE | 是否自动更新关联 |
| ASSOCIATION\_AUTOCREATE | 是否自动创建关联 |
| ASSOCIATION\_SAVE\_REFERENCE | 是否引用自动保存的关联 |
| PRELOAD | 是否自动预加载关联 |

