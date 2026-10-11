


---
created: "2026-10-11"
tags:
  - 未理解
---

# enum

## 📌 一言でいうと

データベースに保存する数値などに、わかりやすい名前を付けて管理できるRailsの機能。

例えば、掲示板の公開状態を次のように管理できる。

- `0` → 下書き（draft）
- `1` → 公開（published）
- `2` → 非公開（archived）

本来は数値で管理するデータを、`draft` や `published` といった名前で扱えるようになる。


## 📖 仕組み・役割

### ① enumの基本的な仕組み

例えば、掲示板（Board）に「公開状態」を管理する `status` というカラムがあるとする。

```ruby
class Board < ApplicationRecord
  enum :status, {
    draft: 0,
    published: 1,
    archived: 2
  }
end
```

|コード|意味|
|---|---|
|`enum`|複数の決められた状態を定義する|
|`:status`|状態を管理するカラム名|
|`draft: 0`|下書きを0で管理|
|`published: 1`|公開を1で管理|
|`archived: 2`|非公開を2で管理|

データベースには数値が保存されるが、Railsでは名前を使って操作できる。

### ② enumを設定すると便利なメソッドが自動生成される

例えば、以下のようなメソッドが使えるようになる。

```ruby
board = Board.new(status: :draft)

board.draft?
# => true

board.published?
# => false
```

さらに、状態を変更するメソッドも使える。

```ruby
board.published!
```

`published!` を実行すると、`status` が `published` に変更され、データベースにも保存される。


### ③ enumで自動生成される主なメソッド

|メソッド|役割|
|---|---|
|`board.draft?`|下書き状態か確認|
|`board.published?`|公開状態か確認|
|`board.published!`|公開状態に変更して保存|
|`Board.draft`|下書きの掲示板を検索|
|`Board.published`|公開中の掲示板を検索|
|`board.status`|現在の状態名を文字列で取得|

つまり、状態の確認・変更・検索が簡単になる。


## 💻 コード例

### ① データベースにstatusカラムを追加

マイグレーションファイル：

```ruby
class AddStatusToBoards < ActiveRecord::Migration[8.0]
  def change
    add_column :boards, :status, :integer,
               default: 0, null: false
  end
end
```

`status` という整数型カラムを追加する。
`default: 0` は初期値を下書きにするための設定。


### ② Modelにenumを定義

ファイル：`app/models/board.rb`

```ruby
class Board < ApplicationRecord
  enum :status, {
    draft: 0,
    published: 1,
    archived: 2
  }
end
```

### ③ 掲示板を作成する

```ruby
board = Board.create!(
  title: "Railsの勉強",
  body: "今日はenumを勉強しました",
  status: :draft
)
```

この場合、データベースの `status` カラムには `0` が保存される。


### ④ 状態を確認する

```ruby
board.draft?
# => true

board.published?
# => false
```

### ⑤ 公開状態に変更する

```ruby
board.published!
```

これで `status` が `1` に変更され、保存される。


### ⑥ 公開中の掲示板だけ取得する

```ruby
@boards = Board.published
```

コントローラーでこのように書けば、公開状態の掲示板だけを取得できる。


### ⑦ Viewで状態によって表示を変える

```ruby
<% if board.published? %>
  <p>公開中</p>
<% else %>
  <p>公開されていません</p>
<% end %>
```

`enum` 自体はModelに定義するが、自動生成されたメソッドはControllerやViewでも使える。


## 🎯 使用場面

① 掲示板や記事の公開状態

- 下書き（draft）
- 公開（published）
- 非公開（archived）


② 注文の進行状況

- 注文受付（pending）
- 発送済み（shipped）
- 配達完了（delivered）


③ ユーザーの権限管理

- 一般ユーザー（general）
- 管理者（admin）


④ タスクの進捗状況

- 未着手（todo）
- 進行中（in_progress）
- 完了（completed）


このように、あらかじめ決まった複数の状態から1つを選んで管理したい場合に使う。


## ⚠️ 注意点・よくある間違い

### ① enumを定義するだけではカラムは作成されない

```ruby
enum :status, {
  draft: 0,
  published: 1
}
```

これはModelで状態の対応関係を定義しているだけ。
データベース側には、別途 `status` カラムが必要。


### ② `status` の値が文字列で返ることに注意

```ruby
board = Board.new(status: :draft)

board.status
# => "draft"
```

整数型のカラムを使っていても、Railsで取得する状態名は文字列になる。


### ③ `published!` と `published?` は違う

```ruby
board.published?
```

公開状態かどうかを確認する。

```ruby
board.published!
```

公開状態に変更し、データベースへ保存する。
`?` は確認、`!` は状態変更と覚える。
ただし、Ruby全般で `!` が必ず保存を意味するわけではない。


### ④ enumの数値を安易に変更しない

```ruby
enum :status, {
  draft: 0,
  published: 1
}
```

運用途中で以下のように変更すると危険。

```ruby
enum :status, {
  draft: 1,
  published: 0
}
```

既存データの意味が逆転してしまう。
整数型enumでは、一度設定した数値の対応関係を維持することが重要。


### ⑤ enumとvalidationは役割が違う

- `enum`：データが取り得る状態を定義する
- `validates`：保存時などにデータのルールを検証する


Rails 8系では、定義されていないenum値を代入すると、標準では `ArgumentError` が発生する。

```ruby
board.status = :unknown
# => ArgumentError
```

`validate: true` を指定すると、不正な値を代入時に例外にせず、バリデーションエラーとして扱える。

```ruby
enum :status, {
  draft: 0,
  published: 1
}, validate: true
```

## 🔗 関連する知識

- [[Model]]
- [[ActiveRecord]]
- [[Migration]]
- [[カラム]]
- [[integer]]
- [[symbol]]
- [[validation]]
- [[validates]]
- [[scope]]
- [[where]]
- [[create]]
- [[save]]
- [[update]]
- [[boolean]]



## 💡 自分がつまずいたところ

- `enum` は状態の名前を定義するだけでなく、便利なメソッドも自動生成すること
- データベースに保存される数値と、Railsで扱う名前の違い
- `published?` と `published!` の違い
- `enum` をModelに書いても、ControllerやViewからメソッドを利用できること
- `enum` と `validates` の役割の違い


## ✅ 理解度チェック
- [ ] 自分の言葉で説明できる
- [ ] コードを読んで理解できる
- [ ] 自分でコードを書ける


## 🔄 復習メモ

### Q1. `enum` は何をするメソッド？

Modelでデータの状態を定義し、それぞれの値をわかりやすい名前で管理できるようにするメソッド。

### Q2. `enum` はどこに書く？

基本的にModelに書く。

### Q3. `enum :status, { draft: 0, published: 1 }` はどういう意味？

`status` というカラムについて、`draft` を0、`published` を1として管理する。

### Q4. `board.published?` は何をする？

掲示板が公開状態かどうかを確認し、`true` または `false` を返す。

### Q5. `board.published!` は何をする？

掲示板の状態を公開に変更し、データベースに保存する。

### Q6. `Board.published` は何をする？

公開状態になっている掲示板のレコードを検索する。

### Q7. `enum` を設定すればデータベースのカラムも自動作成される？

いいえ。カラムはマイグレーションなどで別途作成する必要がある。