


---
created: "2026-10-13"
tags:
  - 未理解
---

# Decorator

## 📌 一言でいうと

**画面に表示するために、データの見せ方を整える処理をまとめるもの。**

**分類：表示に関する処理（Viewを補助する役割）**

※以下はRailsで **Draperというgemを使う場合**の説明

## 📖 仕組み・役割

Decoratorは、元のオブジェクトを包み、機能を追加する設計パターン  
RailsのDraperでは、主にモデルに表示用の機能を追加するために使う

例えば、別々に保存された「姓」と「名」を「太田 裕也」と表示したい場合、名前をつなげる処理をDecoratorにまとめる

**表示用の加工処理を分けることで、ModelやViewの役割を整理できる**

|分類|役割|
|---|---|
|Model|データや業務上のルールを扱う|
|Controller|リクエストを受け取り、必要な処理を進める|
|Decorator|表示用にデータを整える|
|View|HTMLなどを使って画面を作る|

## 💻 コード例

### ① Decoratorに表示用のメソッドを定義する

```ruby
# app/decorators/user_decorator.rb
class UserDecorator < Draper::Decorator
  delegate_all

  def full_name
    "#{object.last_name} #{object.first_name}"
  end
end
```

※Userモデルに `last_name` と `first_name` がある前提の例

|コード|意味|
|---|---|
|`UserDecorator`|Userの表示用処理をまとめるクラス|
|`Draper::Decorator`|Draperが提供する継承元のクラス|
|`delegate_all`|Decoratorにないメソッドの呼び出しを元のオブジェクトに任せる|
|`object`|Decoratorが包んでいる元のオブジェクト|
|`full_name`|この例で自分で定義した、姓と名をつなげるメソッド|

### ② Viewで呼び出す

```ruby
<%= @user.decorate.full_name %>
```

`@user` にユーザーが入っている場合、次の順番で処理される

1. `.decorate` でユーザーをDecoratorで包む
2. `.full_name` で姓と名をつなげた文字列を取得する
3. `<%= %>` で画面に出力する

```ruby
表示例：大阪 太郎
```

Draperでは、モデルに対して `decorate` を呼ぶことで対応するDecoratorを利用でき


## 🎯 使用場面

- 姓と名をつなげて表示する
- 日付を「2026年10月13日」のような形式に整える
- 状態に応じて「公開中」「非公開」を表示する
- 同じ表示用の処理を複数の画面で使う

## ⚠️ 注意点・よくある間違い

### ① Decoratorと`decorate`は別

- **Decorator**：オブジェクトを包み、機能を追加する設計パターン
- **`decorate`**：Draperで対応するDecoratorを利用するためのメソッド

### ② `full_name`は自分で定義したメソッド

Draperが自動で用意するメソッドではない  
今回の例では、`def full_name` で処理を定義している

### ③ 表示用の文字列を作っても、保存データは変わらない

今回の `full_name` は姓と名をつなげた文字列を返すだけ  
データベースへの保存や更新は行っていない

### ④ 利用するライブラリによって書き方が異なる

この例の `Draper::Decorator` や `decorate` はDraperの導入が前提  
すべてのRailsアプリで、そのまま使えるわけではない

## 🔗 関連する知識

- [[Model]]
- [[View]]
- [[Controller]]
- [[Helper]]
- [[Draper]]
- [[decorate]]
- [[delegate_all]]
- [[文字列展開]]




## 💡 自分がつまずいたところ

<!-- 学習中に迷ったことを記入する -->


## ✅ 理解度チェック
- [ ] 自分の言葉で説明できる
- [ ] コードを読んで理解できる
- [ ] 自分でコードを書ける


## 🔄 復習メモ

### Q1. RailsのDraperでは、Decoratorを何に使う？

モデルに表示用の機能を追加し、データの見せ方を整えるために使う

### Q2. DraperのDecoratorはどこに書く？

`app/decorators/` の中に書く

### Q3. `object` は何を指す？

Decoratorが包んでいる元のオブジェクト

### Q4. `@user.decorate.full_name` は何をする？

ユーザーをDecoratorで包み、そこで定義した `full_name` を呼び出す

### Q5. `full_name` は最初から用意されている？

今回の例では、自分で定義したメソッド