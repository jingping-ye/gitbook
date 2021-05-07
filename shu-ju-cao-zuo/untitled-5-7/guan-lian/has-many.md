# Has Many

## 1. Has Many <a id="has-many"></a>

### 1.1. 一对多 <a id="&#x4E00;&#x5BF9;&#x591A;"></a>

 `has many` 关联就是创建和另一个模型的一对多关系， 不像 `has one`，所有者可以拥有0个或多个模型实例。

例如，如果你的应用包含用户和信用卡， 并且每一个用户都拥有多张信用卡。

```text
// 用户有多张信用卡，UserID 是外键
type User struct {
    gorm.Model
    CreditCards []CreditCard
}

type CreditCard struct {
    gorm.Model
    Number   string
    UserID  uint
}
```

### 1.2. 外键 <a id="&#x5916;&#x952E;"></a>

为了定义一对多关系， 外键是必须存在的，默认外键的名字是所有者类型的名字加上它的主键。

就像上面的例子，为了定义一个属于`User` 的模型，外键就应该为 `UserID`。

使用其他的字段名作为外键， 你可以通过 `foreignkey` 来定制它， 例如:

```text
type User struct {
    gorm.Model
    CreditCards []CreditCard `gorm:"foreignkey:UserRefer"`
}

type CreditCard struct {
    gorm.Model
    Number    string
  UserRefer uint
}
```

### 1.3. 外键关联 <a id="&#x5916;&#x952E;&#x5173;&#x8054;"></a>

GORM 通常使用所有者的主键作为外键的值， 在上面的例子中，它就是 `User` 的 `ID`。

当你分配信用卡给一个用户， GORM 将保存用户 `ID` 到信用卡表的 `UserID` 字段中。

你能通过 `association_foreignkey` 来改变它， 例如:

```text
type User struct {
    gorm.Model
  MemberNumber string
    CreditCards  []CreditCard `gorm:"foreignkey:UserMemberNumber;association_foreignkey:MemberNumber"`
}

type CreditCard struct {
    gorm.Model
    Number           string
  UserMemberNumber string
}
```

### 1.4. 多态关联 <a id="&#x591A;&#x6001;&#x5173;&#x8054;"></a>

支持多态的一对多和一对一关联。

```text
  type Cat struct {
    ID    int
    Name  string
    Toy   []Toy `gorm:"polymorphic:Owner;"`
  }

  type Dog struct {
    ID   int
    Name string
    Toy  []Toy `gorm:"polymorphic:Owner;"`
  }

  type Toy struct {
    ID        int
    Name      string
    OwnerID   int
    OwnerType string
  }
```

注意：多态属于和多对多是明确不支持并会抛出错误的。

### 1.5. 使用一对多 <a id="&#x4F7F;&#x7528;&#x4E00;&#x5BF9;&#x591A;"></a>

你可以通过`Related` 找到 `has many` 关联关系。

```text
db.Model(&user).Related(&emails)
//// SELECT * FROM emails WHERE user_id = 111; // 111 是用户表的主键
```

更多高级用法， 请参考 [Association Mode](https://github.com/jinzhu/gorm.io/blob/master/docs/associations.html#Association-Mode)

