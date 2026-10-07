


---
created: "2026-10-07"
tags:
  - 未理解
---

# uniqueness

## 📌 一言でいうと

**同じ値を重複して登録できないようにするバリデーション**

`validates` と組み合わせて、同じ値の重複を防ぐために使う


## 📖 仕組み・役割

例えば
```ruby
validates :email, uniqueness: true
````
と書くと、

**同じメールアドレスがすでに登録されている場合、そのデータは保存できない**

というルールになる

例えばすでに
test@example.com

というメールアドレスが登録されている状態で、もう一度同じメールアドレスを保存しようとすると、バリデーションに失敗する



## 💻 コード例

```ruby
class User < ApplicationRecord
  validates :email, uniqueness: true
end
```

よく使う組み合わせ
```ruby
validates :email, presence: true, uniqueness: true
```

これは
- `presence: true`  
    → **空欄は禁止**
- `uniqueness: rue`  
    → **重複は禁止**
    
という意味


## 🎯 使用場面

- メールアドレスの重複を防ぎたい
- ユーザー名の重複を防ぎたい
- 商品コードを重複させたくない
- 一意である必要があるデータを扱うとき


## ⚠️ 注意点・よくある間違い

### ① `uniqueness` は単独では使わない

基本的には `validates` と一緒に使う
```ruby
validates :email, uniqueness: true
```

### ② 空欄を禁止する機能ではない
```ruby
validates :email, uniqueness: true
```

だけでは、空欄を禁止するルールにはならない

空欄も禁止したい場合は
```ruby
validates :email, presence: true, uniqueness: true
```
と書く


### ③ データベース側の重複防止とは別

`uniqueness: true` はRails側で行うチェック

より確実に重複を防ぎたい場合は、データベース側でも `unique index` を設定する


## 🔗 関連する知識
- [[validates]]
- [[validation]]
- [[presence]]
- [[Model]]
- [[unique index]]


## 💡 自分がつまずいたところ

`uniqueness` は「必須」という意味ではない

役割は、

**同じ値がすでに存在していないかチェックすること**

空欄を禁止したい場合は `presence` を使う


## ✅ 理解度チェック
- [ ] 自分の言葉で説明できる
- [ ] コードを読んで理解できる
- [ ] 自分でコードを書ける


## 🔄 復習メモ

### Q1. `uniqueness: true` は何をする？

同じ値がすでに存在していないかチェックし、重複を防ぐ

### Q2. `uniqueness` はどこで使う？

Modelの `validates` と組み合わせて使う

### Q3. `uniqueness: true` だけで空欄も禁止できる？

できない
空欄を禁止したい場合は `presence: true` も使う

### Q4. `uniqueness` と `presence` の違いは？

- `uniqueness` → 重複していないか確認
- `presence` → 空欄ではないか確認

### Q5. Railsの `uniqueness` だけで重複対策は完全？

完全ではない

確実に重複を防ぐには、データベース側にも `unique index` を設定する