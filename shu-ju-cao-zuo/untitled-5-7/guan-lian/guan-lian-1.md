# 关联

## 1. 关联 <a id="&#x5173;&#x8054;"></a>

### 1.1. 自动创建/更新 <a id="&#x81EA;&#x52A8;&#x521B;&#x5EFA;&#x66F4;&#x65B0;"></a>

GORM 将在创建或保存一条记录的时候自动保存关联和它的引用，如果关联有一个主键， GORM 将调用 `Update` 来更新它， 不然，它将会被创建。

```text
user := User{
    Name:            "jinzhu",
    BillingAddress:  Address{Address1: "Billing Address - Address 1"},
    ShippingAddress: Address{Address1: "Shipping Address - Address 1"},
    Emails:          []Email{
        {Email: "jinzhu@example.com"},
        {Email: "jinzhu-2@example@example.com"},
    },
    Languages:       []Language{
        {Name: "ZH"},
        {Name: "EN"},
    },
}

db.Create(&user)
//// BEGIN TRANSACTION;
//// INSERT INTO "addresses" (address1) VALUES ("Billing Address - Address 1");
//// INSERT INTO "addresses" (address1) VALUES ("Shipping Address - Address 1");
//// INSERT INTO "users" (name,billing_address_id,shipping_address_id) VALUES ("jinzhu", 1, 2);
//// INSERT INTO "emails" (user_id,email) VALUES (111, "jinzhu@example.com");
//// INSERT INTO "emails" (user_id,email) VALUES (111, "jinzhu-2@example.com");
//// INSERT INTO "languages" ("name") VALUES ('ZH');
//// INSERT INTO user_languages ("user_id","language_id") VALUES (111, 1);
//// INSERT INTO "languages" ("name") VALUES ('EN');
//// INSERT INTO user_languages ("user_id","language_id") VALUES (111, 2);
//// COMMIT;

db.Save(&user)
```

### 1.2. 关闭自动更新 <a id="&#x5173;&#x95ED;&#x81EA;&#x52A8;&#x66F4;&#x65B0;"></a>

如果你的关联记录已经存在在数据库中， 你可能会不想去更新它。

你可以设置 `gorm:association_autoupdate` 为 `false`

```text
// 不更新有主键的关联，但会更新引用
db.Set("gorm:association_autoupdate", false).Create(&user)
db.Set("gorm:association_autoupdate", false).Save(&user)
```

或者使用 GORM 的标签， `gorm:"association_autoupdate:false"`

```text
type User struct {
  gorm.Model
  Name       string
  CompanyID  uint
  // 不更新有主键的关联，但会更新引用
  Company    Company `gorm:"association_autoupdate:false"`
}
```

### 1.3. 关闭自动创建 <a id="&#x5173;&#x95ED;&#x81EA;&#x52A8;&#x521B;&#x5EFA;"></a>

即使你禁用了 `AutoUpdating`, 仍然会创建没有主键的关联，并保存它的引用。

你可以通过把 `gorm:association_autocreate` 设置为 `false` 来禁用这个行为。

```text
// 不创建没有主键的关联，不保存它的引用。
db.Set("gorm:association_autocreate", false).Create(&user)
db.Set("gorm:association_autocreate", false).Save(&user)
```

或者使用 GORM 标签， `gorm:"association_autocreate:false"`

```text
type User struct {
  gorm.Model
  Name       string
  // 不创建没有主键的关联，不保存它的引用。
  Company1   Company `gorm:"association_autocreate:false"`
}
```

### 1.4. 关闭自动创建/更新 <a id="&#x5173;&#x95ED;&#x81EA;&#x52A8;&#x521B;&#x5EFA;&#x66F4;&#x65B0;"></a>

禁用 `AutoCreate` 和 `AutoUpdate`，你可以一起使用它们两个的设置。

```text
db.Set("gorm:association_autoupdate", false).Set("gorm:association_autocreate", false).Create(&user)

type User struct {
  gorm.Model
  Name    string
  Company Company `gorm:"association_autoupdate:false;association_autocreate:false"`
}
```

或者使用 `gorm:save_associations`

```text
db.Set("gorm:save_associations", false).Create(&user)
db.Set("gorm:save_associations", false).Save(&user)

type User struct {
  gorm.Model
  Name    string
  Company Company `gorm:"association_autoupdate:false"`
}
```

### 1.5. 关闭保存引用 <a id="&#x5173;&#x95ED;&#x4FDD;&#x5B58;&#x5F15;&#x7528;"></a>

如果你不想当更新或保存数据的时候保存关联的引用， 你可以使用下面的技巧

```text
db.Set("gorm:association_save_reference", false).Save(&user)
db.Set("gorm:association_save_reference", false).Create(&user)
```

或者使用标签

```text
type User struct {
  gorm.Model
  Name       string
  CompanyID  uint
  Company    Company `gorm:"association_save_reference:false"`
}
```

### 1.6. 关联模式 <a id="&#x5173;&#x8054;&#x6A21;&#x5F0F;"></a>

关联模式包含一些可以轻松处理与关系相关的事情的辅助方法。

```text
// 开启关联模式
var user User
db.Model(&user).Association("Languages")
// `user` 是源表，必须包含主键
// `Languages` 是源表关系字段名称。
// 只有上面两个条件都能匹配，关联模式才会生效， 检查是否正常：
// db.Model(&user).Association("Languages").Error
```

#### 1.6.1. 查找关联 <a id="&#x67E5;&#x627E;&#x5173;&#x8054;"></a>

查找匹配的关联

```text
db.Model(&user).Association("Languages").Find(&languages)
```

#### 1.6.2. 增加关联 <a id="&#x589E;&#x52A0;&#x5173;&#x8054;"></a>

为 `many to many`， `has many` 新增关联， 为 `has one`, `belongs to` 替换当前关联

```text
db.Model(&user).Association("Languages").Append([]Language{languageZH, languageEN})
db.Model(&user).Association("Languages").Append(Language{Name: "DE"})
```

#### 1.6.3. 替换关联 <a id="&#x66FF;&#x6362;&#x5173;&#x8054;"></a>

用一个新的关联替换当前关联

```text
db.Model(&user).Association("Languages").Replace([]Language{languageZH, languageEN})
db.Model(&user).Association("Languages").Replace(Language{Name: "DE"}, languageEN)
```

#### 1.6.4. 删除关联 <a id="&#x5220;&#x9664;&#x5173;&#x8054;"></a>

删除源和参数对象之间的关系， 只会删除引用，不会删除他们在数据库中的对象。

```text
db.Model(&user).Association("Languages").Delete([]Language{languageZH, languageEN})
db.Model(&user).Association("Languages").Delete(languageZH, languageEN)
```

#### 1.6.5. 清理关联 <a id="&#x6E05;&#x7406;&#x5173;&#x8054;"></a>

删除源和当前关联之间的引用，不会删除他们的关联

```text
db.Model(&user).Association("Languages").Clear()
```

#### 1.6.6. 统计关联 <a id="&#x7EDF;&#x8BA1;&#x5173;&#x8054;"></a>

返回当前关联的统计数

```text
db.Model(&user).Association("Languages").Count()
```

