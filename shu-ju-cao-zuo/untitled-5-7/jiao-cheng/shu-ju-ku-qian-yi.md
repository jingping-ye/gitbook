# 数据库迁移

## 1. 数据库迁移 <a id="&#x6570;&#x636E;&#x5E93;&#x8FC1;&#x79FB;"></a>

### 1.1. 自动迁移 <a id="&#x81EA;&#x52A8;&#x8FC1;&#x79FB;"></a>

使用 migrate 来维持你的表结构一直处于最新状态。

警告：migrate 仅支持创建表、增加表中没有的字段和索引。为了保护你的数据，它并不支持改变已有的字段类型或删除未被使用的字段

```text
db.AutoMigrate(&User{})

db.AutoMigrate(&User{}, &Product{}, &Order{})

// 创建表的时候，添加表后缀
db.Set("gorm:table_options", "ENGINE=InnoDB").AutoMigrate(&User{})
```

### 1.2. 其他数据库迁移工具 <a id="&#x5176;&#x4ED6;&#x6570;&#x636E;&#x5E93;&#x8FC1;&#x79FB;&#x5DE5;&#x5177;"></a>

GORM 的数据库迁移工具能够支持主要的数据库，但是如果你要寻找更多的迁移工具， GORM 会提供的数据库接口，这可能可以给到你帮助。

```text
// 返回 `*sql.DB`
db.DB()
```

参考 [通用接口](https://github.com/jinzhu/gorm.io/blob/master/docs/generic_interface.html) 以获得更多详细说明

### 1.3. 表结构的方法 <a id="&#x8868;&#x7ED3;&#x6784;&#x7684;&#x65B9;&#x6CD5;"></a>

#### 1.3.1. Has Table <a id="has-table"></a>

```text
// 检查模型中 User 表是否存在
db.HasTable(&User{})

// 检查 users 表是否存在
db.HasTable("users")
```

#### 1.3.2. Create Table <a id="create-table"></a>

```text
// 通过模型 User 创建表
db.CreateTable(&User{})

// 在创建 users 表的时候，会在 SQL 语句中拼接上 `"ENGINE=InnoDB"`
db.Set("gorm:table_options", "ENGINE=InnoDB").CreateTable(&User{})
```

#### 1.3.3. Drop table <a id="drop-table"></a>

```text
// 删除模型 User 表
db.DropTable(&User{})

// 删除 users 表
db.DropTable("users")

// 删除模型 User 表和 products 表
db.DropTableIfExists(&User{}, "products")
```

#### 1.3.4. ModifyColumn <a id="modifycolumn"></a>

以给定的值来定义字段类型

```text
// User 模型，改变 description 字段的数据类型为 `text`
db.Model(&User{}).ModifyColumn("description", "text")
```

#### 1.3.5. DropColumn <a id="dropcolumn"></a>

```text
// User 模型，删除  description 字段
db.Model(&User{}).DropColumn("description")
```

#### 1.3.6. Add Indexes <a id="add-indexes"></a>

```text
// 为 `name` 字段建立一个名叫 `idx_user_name` 的索引
db.Model(&User{}).AddIndex("idx_user_name", "name")

// 为 `name`, `age` 字段建立一个名叫 `idx_user_name_age` 的索引
db.Model(&User{}).AddIndex("idx_user_name_age", "name", "age")

// 添加一条唯一索引
db.Model(&User{}).AddUniqueIndex("idx_user_name", "name")

// 为多个字段添加唯一索引
db.Model(&User{}).AddUniqueIndex("idx_user_name_age", "name", "age")
```

#### 1.3.7. Remove Index <a id="remove-index"></a>

```text
// 移除索引
db.Model(&User{}).RemoveIndex("idx_user_name")
```

#### 1.3.8. Add Foreign Key <a id="add-foreign-key"></a>

```text
// 添加主键
// 第一个参数 : 主键的字段
// 第二个参数 : 目标表的 ID
// 第三个参数 : ONDELETE
// 第四个参数 : ONUPDATE
db.Model(&User{}).AddForeignKey("city_id", "cities(id)", "RESTRICT", "RESTRICT")
```

#### 1.3.9. Remove ForeignKey <a id="remove-foreignkey"></a>

```text
db.Model(&User{}).RemoveForeignKey("city_id", "cities(id)")
```

