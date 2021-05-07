# Many To Many

## 1. Many To Many <a id="many-to-many"></a>

### 1.1. 多对多 <a id="&#x591A;&#x5BF9;&#x591A;"></a>

多对多为两个模型增加了一个中间表。

例如，如果你的应用包含用户和语言， 一个用户会说多种语言，并且很多用户会说一种特定的语言。

```text
// 用户拥有并属于多种语言， 使用  `user_languages` 作为中间表
type User struct {
    gorm.Model
    Languages         []Language `gorm:"many2many:user_languages;"`
}

type Language struct {
    gorm.Model
    Name string
}
```

### 1.2. 反向关联 <a id="&#x53CD;&#x5411;&#x5173;&#x8054;"></a>

```text
// 用户拥有并且属于多种语言，使用 `user_languages` 作为中间表
type User struct {
    gorm.Model
    Languages         []*Language `gorm:"many2many:user_languages;"`
}

type Language struct {
    gorm.Model
    Name string
    Users               []*User     `gorm:"many2many:user_languages;"`
}

db.Model(&language).Related(&users)
//// SELECT * FROM "users" INNER JOIN "user_languages" ON "user_languages"."user_id" = "users"."id" WHERE  ("user_languages"."language_id" IN ('111'))
```

### 1.3. 外键 <a id="&#x5916;&#x952E;"></a>

```text
type CustomizePerson struct {
  IdPerson string             `gorm:"primary_key:true"`
  Accounts []CustomizeAccount `gorm:"many2many:PersonAccount;association_foreignkey:idAccount;foreignkey:idPerson"`
}

type CustomizeAccount struct {
  IdAccount string `gorm:"primary_key:true"`
  Name      string
}
```

外键会为两个结构体创建一个多对多的关系，并且这个关系将通过外键`customize_person_id_person` 和 `customize_account_id_account` 保存到中间表 `PersonAccount`。

### 1.4. 中间表外键 <a id="&#x4E2D;&#x95F4;&#x8868;&#x5916;&#x952E;"></a>

如果你想改变中间表的外键，你可以用标签 `association_jointable_foreignkey`, `jointable_foreignkey`

```text
type CustomizePerson struct {
  IdPerson string             `gorm:"primary_key:true"`
  Accounts []CustomizeAccount `gorm:"many2many:PersonAccount;foreignkey:idPerson;association_foreignkey:idAccount;association_jointable_foreignkey:account_id;jointable_foreignkey:person_id;"`
}

type CustomizeAccount struct {
  IdAccount string `gorm:"primary_key:true"`
  Name      string
}
```

### 1.5. 自引用 <a id="&#x81EA;&#x5F15;&#x7528;"></a>

为了定义一个自引用的多对多关系，你不得不改变中间表的关联外键。

和来源表外键不同的是它是通过结构体的名字和主键生成的，例如：

```text
type User struct {
  gorm.Model
  Friends []*User `gorm:"many2many:friendships;association_jointable_foreignkey:friend_id"`
}
```

GORM 将创建一个带外键 `user_id` 和 `friend_id` 的中间表， 并且使用它去保存用户表的自引用关系。

然后你可以像普通关系一样操作它， 例如：

```text
DB.Preload("Friends").First(&user, "id = ?", 1)

DB.Model(&user).Association("Friends").Append(&User{Name: "friend1"}, &User{Name: "friend2"})

DB.Model(&user).Association("Friends").Delete(&User{Name: "friend2"})

DB.Model(&user).Association("Friends").Replace(&User{Name: "new friend"})

DB.Model(&user).Association("Friends").Clear()

DB.Model(&user).Association("Friends").Count()
```

### 1.6. 使用多对多 <a id="&#x4F7F;&#x7528;&#x591A;&#x5BF9;&#x591A;"></a>

```text
db.Model(&user).Related(&languages, "Languages")
//// SELECT * FROM "languages" INNER JOIN "user_languages" ON "user_languages"."language_id" = "languages"."id" WHERE "user_languages"."user_id" = 111

//  当查询用户时预加载 Language
db.Preload("Languages").First(&user)
```

更多高级用法， 请参考 [Association Mode](https://github.com/jinzhu/gorm.io/blob/master/docs/associations.html#Association-Mode)

