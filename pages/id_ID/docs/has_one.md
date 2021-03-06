---
title: Memiliki Satu
layout: page
---

## Has One

A`memiliki satu`asosiasi juga membuat koneksi satu -lawan-satu dengan model lain, tapi dengan beberapa semantik yang berbeda (dan konsekuensinya). Asosiasi ini menunjukkan bahwa setiap contoh dari model mengandung atau memiliki satu contoh model lain.

For example, if your application includes users and credit cards, and each user can only have one credit card.

```go
// User has one CreditCard, CreditCardID is the foreign key
type CreditCard struct {
    gorm.Model
    Number   string
    UserID   uint
}

type User struct {
    gorm.Model
    CreditCard   CreditCard
}
```

## Foreign Key

For a `has one` relationship, a foreign key field must also exist, the owned will save the primary key of the model belongs to it into this field.

The field's name is usually generated with `has one` model's type plus its `primary key`, for the above example it is `UserID`.

When you give a credit card to the user, its will save the User's `ID` into its `UserID` field.

If you want to use another field to save the relationship, you can change it with tag `foreignkey`, e.g:

```go
type CreditCard struct {
    gorm.Model
    Number   string
    UserName string
}

type User struct {
    gorm.Model
    CreditCard CreditCard `gorm:"foreignkey:UserName"`
}
```

## Association ForeignKey

By default, the owned entity will save the `has one` model's primary into a foreign key, you could change to save another field, like use `Name` for below example.

```go
type CreditCard struct {
    gorm.Model
    Number string
    UID    string
}

type User struct {
    gorm.Model
    Name       `sql:"index"`
    CreditCard CreditCard `gorm:"foreignkey:uid;association_foreignkey:name"`
}
```

## Asosiasi Polimorfisme

Supports polymorphic `has many` and `has one` associations.

```go
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

Catatan: polimorfik milik-ke dan banyak-ke-banyak secara eksplisit TIDAK didukung, dan akan membuang kesalahan.

## Working with Has One

You could find `has one` associations with `Related`

```go
var card CreditCard
db.Model(&user).Related(&card, "CreditCard")
//// SELECT * FROM credit_cards WHERE user_id = 123; // 123 is user's primary key
// CreditCard is user's field name, it means get user's CreditCard relations and fill it into variable card
// If the field name is same as the variable's type name, like above example, it could be omitted, like:
db.Model(&user).Related(&card)
```

Untuk penggunaan lanjutan, lihat [Mode Asosiasi](/docs/associations.html#Association-Mode)