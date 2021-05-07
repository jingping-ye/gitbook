# Has One

## 1. Has One <a id="has-one"></a>

### 1.1. Has One <a id="has-one_1"></a>

`has one` 关联也是与另一个模型建立一对一的连接，但语义（和结果）有些不同。 此关联表示模型的每个实例包含或拥有另一个模型的一个实例。

例如，如果你的应用程序包含用户和信用卡，并且每个用户只能有一张信用卡。

```text
// 用户有一个信用卡，CredtCardID 外键
type User struct {
    gorm.Model
    CreditCard   CreditCard
}

type CreditCard struct {
    gorm.Model
    Number   string
    UserID    uint
}
```

### 1.2. 外键 <a id="&#x5916;&#x952E;"></a>

对于一对一关系，一个外键字段也必须存在，所有者将保存主键到模型关联的字段里。

这个字段的名字通常由 `belongs to model` 的类型加上它的 `primary key` 产生的，就上面的例子而言，它就是 `CreditCardID`

当你给用户一个信用卡， 它将保存一个信用卡的 `ID` 到 `CreditCardID` 字段中。

如果你想使用另一个字段来保存这个关系，你可以通过使用标签 `foreignkey` 来改变它， 例如：

```text
type User struct {
  gorm.Model
  CreditCard CreditCard `gorm:"foreignkey:CardRefer"`
}

type CreditCard struct {
    gorm.Model
    Number      string
    UserName string
}
```

### 1.3. 关联外键 <a id="&#x5173;&#x8054;&#x5916;&#x952E;"></a>

通常，所有者会保存 `belogns to model` 的主键到外键，你可以改为保存其他字段， 就像下面的例子使用 `Number` 。

```text
type User struct {
  gorm.Model
  CreditCard CreditCard `gorm:"association_foreignkey:Number"`
}

type CreditCard struct {
    gorm.Model
    Number string
    UID       string
}
```

### 1.4. 多态关联 <a id="&#x591A;&#x6001;&#x5173;&#x8054;"></a>

支持多态的一对多和一对一关联。

```text
  type Cat struct {
    ID    int
    Name  string
    Toy   Toy `gorm:"polymorphic:Owner;"`
  }

  type Dog struct {
    ID   int
    Name string
    Toy  Toy `gorm:"polymorphic:Owner;"`
  }

  type Toy struct {
    ID        int
    Name      string
    OwnerID   int
    OwnerType string
  }
```

注意：多态属于和多对多是明确的不支持并将会抛出错误。

### 1.5. 使用一对一 <a id="&#x4F7F;&#x7528;&#x4E00;&#x5BF9;&#x4E00;"></a>

你可以通过 `Related` 找到 `has one` 关联。

```text
var card CreditCard
db.Model(&user).Related(&card, "CreditCard")
//// SELECT * FROM credit_cards WHERE user_id = 123; // 123 是用户表的主键
// CreditCard  是用户表的字段名，这意味着获取用户的信用卡关系并写入变量 card。
// 像上面的例子，如果字段名和变量类型名一样，它就可以省略， 像：
db.Model(&user).Related(&card)
```

更多高级用法，请参考 [Association Mode](https://github.com/jinzhu/gorm.io/blob/master/docs/associations.html#Association-Mode)

