# Gorm

`gorm` 是一个很强大的 Go 语言数据库。基于 `ORM` 框架提供了多种方式操作数据库。

## 使用

下载依赖：
```sh
go get gorm.io/gorm
```

在文件中添加如下代码连接至数据库：
```go
dsn := "username:password@tcp(127.0.0.1:3306)/database"
db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{})
// db 即为后续操作的入口变量。
```

## 基础操作 `CRUD`

最基本的数据库 增删改查 功能。数据库的入门。

```go
// 详细代码可以查看 https://github.com/pykelysia/gorm-mysql/tree/main/base-opt/asus

// type.go
type (
	Product struct {
        // 定义表单主键，以及主键自增，在插入时就会自动为主键赋值
		ID    int `grom:"primaryKey;sutoIncrement"`
		Name  string
		Price int
	}
)

// main.go
// 创建数据并添加至数据库
products := []*Product{
    {Name: "book", Price: 10},
    {Name: "food", Price: 10},
    {Name: "macbook", Price: 1000},
}
db.Create(&products)

// 查询 price = 10 的行并全部返回
// output: [{1 book 10} {2 food 10}]
var results []Product
db.Where("price = ?", 10).Find(&results)
fmt.Println(results)

// 删除 price = 10 的行，并获取表单中的所有行
// output: [{3 macbook 1000}]
db.Delete(&results)
db.Find(&results)
fmt.Println(results)

// 更新第一行的数据，自动匹配 ID 相同的行，并获取所有行
// output: [{3 macbook 900}]
db.Save(&Product{
    ID:    results[0].ID,
    Name:  results[0].Name,
    Price: 900,
})
db.Find(&results)
fmt.Println(results)
```

## 关联

实际生产过程中，对数据库的操作往往不只局限于单独对一个表单的查询，往往需要多个表单，一起查找。

针对此情景， `gorm` 库提供了两种高级查询的方法：
- 原生 `SELECT` 和 `JOIN`
- `gorm` 内置 `PreLoad` 和 `foreignKey`

### `SELECT` 和 `JOIN`

这种方法是模拟原生的 `SQL` 进行操作。只要学会基本的数据库操作就可以简单上手。

```go
// 详细代码可以查看 https://github.com/pykelysia/gorm-mysql/tree/main/connection/select-join

// 表单内容:
//     companies:
//         [{1 NONE} {2 Apple}]
//     products:
//         [{1 book 10 books 1} {2 food 10 needs 1} {3 macbook 1000 PC 2} {4 wine 50 needs 1}]
// output: [{book NONE} {food NONE} {macbook Apple} {wine NONE}]
type Reslut struct {
    Name        string `gorm:"column:name"`
    CompanyName string `gorm:"column:company_name"`
}
var result []Reslut
// 由于需要接收的数据往往与之前直接自动迁移的数据结构体不同，
// 所以需要先通过 Table() 方法确定 `FROM` 的表单，然后进行操作即可。
db.Table("products").Select("products.name", "companies.company_name").Joins("LEFT JOIN companies ON products.company_id = companies.company_id").Find(&result)
fmt.Println(result)
```

### `PreLoad` 和 `foreignKey`

显然，使用 `SELECT` 和 `JOIN` 的方法不仅原始，还很麻烦复杂，会做很多的重复工作。显然不符合我们使用该框架的初衷。

所以， `gorm` 提供了更为现代的调用方式，也就是 `PreLoad` 和 `foreignKey`，预加载和外键。

相较于原始的 `SELECT` 和 `JOIN`, `PreLoad` 和 `foreignKey` 就比较复杂且不易理解了。

#### `foreignKey` 外键

既然脱离了 `JOIN` 的使用，就需要用一个东西来替代它的作用，而 `foreignKey` 就是起到了这样的一个作用。

一般而言，外键的使用涉及到了数据之间的关系，主要包括；Belongs To，Has One，Has Many，Many To Many。不同的数据逻辑关系，影响到了他们使用外键的方式。

**Belongs To**: 

即“多对一”，比如多个 Product 属于一个 Company。
```go
type (
    Product struct {
        ProductID int
        Company Company `gorm"foreighKey:CompanyID"`
    }
    Company struct {
        CompanyID int
    }
)
```

**Has One**: 

即“一对一”关系，比如一个 Company 只拥有一个 Product。（虽然这个不怎么现实）
```go
type (
	Product struct {
		ID    int    `gorm:"primaryKey;autoIncrement;column:id"`
	}
	Company struct {
		CompanyID   int     `gorm:"primarykey;autoIncrement;column:company_id"`
		Product     Product `gorm:"foreignKey:ID"`
	}
)
```

    从形式上来看， Belongs To 和 Has One 的形式几乎可以说是完全相同，但是仍然有一些细微的差距这一点差距可以在使用 PreLoad 函数时有所体会。

**Has Many**: 

即“一对多”关系，比如一个 Company 有多个 Product。
```go
type (
	Product struct {
		ID        int    `gorm:"primaryKey;autoIncrement;column:id"`
		CompanyID int    `gorm:"column:company_id"`
	}
	Company struct {
		CompanyID   int       `gorm:"primarykey;autoIncrement;column:company_id"`
		Product     []Product `gorm:"foreignKey:CompanyID"`
        // NOTICE: 这里的外键中所指的是 Product.CompanyID，
        //         而非 Company.CompanyID
	}
)
```

**Many To Many**: 

即“多对多”关系，比如一个 Company 可以有多个 Product，而同一个 Product 也可以属于多个 Company 共同生产。
```go
type (
	Product struct {
		ID    int    `gorm:"primaryKey;autoIncrement;column:id"`
	}
	Company struct {
		CompanyID   int       `gorm:"primarykey;autoIncrement;column:company_id"`
		CompanyName string    `gorm:"column:company_name"`
		Product     []Product `gorm:"many2many:company_product;"`
	}
)
```

#### `PreLoad` 预加载

（该部分下的所有详细代码可前往 [GitHub](https://github.com/pykelysia/gorm-mysql/tree/main/connection)查看）

预加载会将表单内容根据外键预先整合在一起，相当于做了一次 `JOIN` 操作。所以对于预加载而言，真正需要理解的是外键的设置。

例如 “Has Many”:

```go
// （type 自行向上查找查看）
var results []Company
db.Preload("Product").Find(&results)
fmt.Println(results)
```