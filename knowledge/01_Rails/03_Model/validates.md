

---
created: "2026-10-07"
tags:
  - 未理解
---

# validates

## 📌 一言でいうと

**データを保存するときのルールを設定するメソッド**

「名前は必須」
「メールアドレスは重複禁止」
「文字数は20文字以内」

などの条件を決められる


## 📖 仕組み・役割

例えば
```ruby
validates :name, presence: true
````
と書くと、

**nameが空の場合は保存できない**
というルールになる

`validates` は、データを保存する前に  
「このデータは条件を満たしているか？」  
をチェックするために使う

## 💻 コード例

```ruby
validates :email, presence: true
validates :email, uniqueness: true
validates :name, length: { maximum: 20 }
```
それぞれ
- `presence: true`  
    → 空欄を禁止する
- `uniqueness: true`  
    → 重複を禁止する
- `length`  
    → 文字数を制限する



## 🎯 使用場面

- 名前を必須にしたい
- メールアドレスの重複を防ぎたい
- パスワードの文字数を決めたい
- 投稿タイトルの文字数を制限したい

など、**保存するデータにルールをつけたいとき**


## ⚠️ 注意点・よくある間違い

### ① `validates` はModelに書く

```ruby
class User < ApplicationRecord
  validates :name, presence: true
end
```
Controllerではなく、基本的にModelに書く

### ② `validates` はデータを保存するメソッドではない

```ruby
validates :name, presence: true
```

これは保存処理ではなく、
**保存してよいデータかチェックするルールを設定しているだけ**

### ③ `validation` と `validates` は別

- `validation`  
    → データをチェックする仕組み全体の名前
- `validates`  
    → バリデーションを設定するためのメソッド

### ④ `validate` とも違う

- `validates`  
    → Railsに用意されているルールを使う
- `validate`  
    → 自分で作った独自のチェック処理を使う

## 🔗 関連する知識

- [[validation]]
- [[validate]]
- [[Model]]
- [[presence]]
- [[uniqueness]]
- [[length]]
- [[save]]
- [[create]]




## 💡 自分がつまずいたところ

`validation`、`validates`、`validate`の違いが分かりにくい

覚え方は
- **validation** → 仕組み全体
- **validates** → 用意されたルールを設定する
- **validate** → 自作ルールを使う

`validates` は、Modelに保存するデータの条件を決めるもの


## ✅ 理解度チェック
- [ ] 自分の言葉で説明できる
- [ ] コードを読んで理解できる
- [ ] 自分でコードを書ける


## 🔄 復習メモ

### Q1. `validates` は何をするメソッド？
Modelに「保存するデータが満たすべきルール」を設定するメソッド

### Q2. `validates` はどこに書く？
基本的にModelに書く

### Q3. `presence: true` はどういう意味？
値が空ではないことを必須にする