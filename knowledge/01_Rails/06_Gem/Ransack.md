

---
created: "2026-10-15"
tags:
  - 未理解
---

# Ransack

## 📌 一言でいうと

**Railsで検索や並べ替えの機能を作りやすくするgem**

名前やメールアドレスなどの検索条件を受け取り、条件に合ったデータを取得する仕組みを組み立てられる

**分類：Model・Controller・Viewにまたがって使うライブラリ**

|分類|担当すること|
|---|---|
|Model|検索や並べ替えを許可する項目を設定する|
|Controller|検索条件を受け取り、一覧表示用のデータ取得を準備する|
|View|検索フォームや検索結果を表示する|


## 📖 仕組み・役割

### gemとは

Rubyで使える機能をまとめたパッケージのこと

Ransackを導入すると、検索機能を作るためのメソッドやビューヘルパーを使えるようになる

### Ransackでできること

- 名前に特定の文字を含むユーザーを検索する
- 指定した金額以上の商品を絞り込む
- 複数の条件を組み合わせて検索する
- 一覧を名前順や作成日時順などに並べ替える

検索フォームには`search_form_for`、並べ替え用のリンクには`sort_link`を使える [activerecord-hackery.github.io](https://activerecord-hackery.github.io/ransack/getting-started/simple-mode/?utm_source=chatgpt.com)

### 検索の基本的な流れ

1. Viewの検索フォームに条件を入力する
2. Controllerが`params[:q]`で検索条件を受け取る
3. `ransack`で検索オブジェクトを作る
4. `result`で検索条件を反映したRelationを取得する
5. Viewで条件に合うデータを一覧表示する

Model側では、検索対象として許可する項目をあらかじめ設定しておく

### 検索条件は「カラム名＋検索述語」で表す

検索述語とは、「含む」「等しい」「以上」など、比較の仕方を指定するもの

```
email_cont
```

この場合は、`email`が検索対象、`cont`が「含む」という条件

|検索条件の例|意味|
|---|---|
|`email_cont`|メールアドレスに指定した文字を含む|
|`email_eq`|メールアドレスが指定した値と等しい|
|`age_gteq`|年齢が指定した値以上|
|`age_lteq`|年齢が指定した値以下|

それぞれ、対象となるカラムがモデルに存在し、検索を許可していることが前提

### `Ransack`と`ransack`の違い

|名前|意味|
|---|---|
|`Ransack`|検索機能を提供するライブラリの名前|
|`ransack`|検索オブジェクトを作るメソッド|
|`result`|検索条件を反映したRelationを返すメソッド|

**Ransackという道具一式の中に、`ransack`や`result`というメソッドがある**と考えると整理しやすい

## 💻 コード例

ユーザーのメールアドレスを検索する例

`User`モデルに`email`カラムがあり、ユーザー一覧のルートが設定されていることが前提
### ① gemを導入する

```ruby
# Gemfile
gem "ransack"
```

追加後、ターミナルで実行する

```
bundle install
```

### ② Modelで検索を許可する項目を設定する

```ruby
# app/models/user.rb
class User < ApplicationRecord
  # 検索や並べ替えに使うことを許可するカラム
  def self.ransackable_attributes(auth_object = nil)
    ["email"]
  end

  # 今回は関連先を使った検索を許可しない
  def self.ransackable_associations(auth_object = nil)
    []
  end
end
```

`ransackable_attributes`は、検索や並べ替えに使えるカラムを指定するメソッド

`ransackable_associations`は、検索に使える関連を指定するメソッド

この例では、ユーザーの`email`だけを検索対象として許可している

### ③ Controllerで検索条件を受け取る

```ruby
# app/controllers/users_controller.rb
class UsersController < ApplicationController
  def index
    # 検索条件をもとに検索オブジェクトを作る
    @q = User.ransack(params[:q])

    # 検索条件を反映したRelationを取得する
    @users = @q.result
  end
end
```

### ④ Viewに検索フォームと結果を表示する

```ruby
<%# app/views/users/index.html.erb %>

<%= search_form_for @q do |f| %>
  <%= f.label :email_cont, "メールアドレスに含まれる文字" %>
  <%= f.search_field :email_cont %>
  <%= f.submit "検索" %>
<% end %>

<% @users.each do |user| %>
  <p><%= user.email %></p>
<% end %>
```

フォームに`example`と入力して検索すると、メールアドレスに`example`を含むユーザーが表示対象になる

## 🎯 使用場面

- ユーザー一覧に検索フォームを追加するとき
- 掲示板をタイトルや本文で検索するとき
- 商品を価格帯や商品名で絞り込むとき
- 管理画面で複数の条件を指定してデータを探すとき
- 一覧に並べ替え機能を追加するとき

**一覧画面に、利用者が条件を指定できる検索機能を付けたい場面**で役立つ


## ⚠️ 注意点・よくある間違い

### 導入するだけで検索画面が完成するわけではない

Ransackは検索機能を作るための道具を提供するもの

Modelの設定、Controllerの検索処理、Viewのフォームや結果表示は自分で用意する

### 検索対象を明示的に許可する必要がある

Ransack 4.0以降では、検索や並べ替えの対象となる項目の許可設定が必要

検索したいカラムが存在していても、許可していなければそのまま検索できるとは限らない [activerecord-hackery.github.io](https://activerecord-hackery.github.io/ransack/getting-started/simple-mode/?utm_source=chatgpt.com)

### 検索を許可する項目と、保存を許可する項目は別

|設定|役割|
|---|---|
|`ransackable_attributes`|検索や並べ替えに使えるカラムを指定する|
|Strong Parametersの`permit`|一括代入で登録・更新に使えるパラメータを指定する|

`email`を検索可能にしたことが、そのままメールアドレスの更新許可になるわけではない

### `cont`はカラム名の一部ではない

```
email_cont
```

`email_cont`というカラムを作るわけではない

`email`というカラムに対して、「指定した文字を含む」という条件を指定している

### `@q`には検索結果の一覧が入るわけではない

```ruby
@q = User.ransack(params[:q])
```

`@q`に入るものは、検索条件を保持する検索オブジェクト

```ruby
@users = @q.result
```

`@users`に入るものは、検索条件を反映した`ActiveRecord::Relation`

通常、実際のレコードの読み込みは、`each`などでデータが必要になった段階で行われる

## 🔗 関連する知識

- [[Bundler]]
- [[Model]]
- [[Controller]]
- [[View]]
- [[ransack・result]]
- [[params]]
- [[search_form_for]]
- [[sort_link]]


## 💡 自分がつまずいたところ

- `Ransack`はgemの名前、`ransack`はそのgemが提供するメソッド
- Ransack全体をModel・Controller・Viewのどれか一つだけに分類する必要はない
- `email_cont`は「カラム名」と「検索の仕方」を組み合わせた指定
- `@q`は検索条件を持つもの、`@users`は検索結果の一覧表示に使うもの
- 検索項目の許可と、データを登録・更新するための許可は別の設定


## ✅ 理解度チェック
- [ ] 自分の言葉で説明できる
- [ ] コードを読んで理解できる
- [ ] 自分でコードを書ける
- [ ] Ransackを使った検索の流れを説明できる
- [ ] Model・Controller・Viewの役割を区別できる
- [ ] `email_cont`をカラム名と検索述語に分けて説明できる


## 🔄 復習メモ

### Q1. Ransackは何のためのgem？

Railsで検索や並べ替えの機能を作りやすくするためのgem

### Q2. RansackはModel・Controller・Viewのどれに分類する？

複数の層にまたがって使うライブラリ

Modelで検索対象を許可し、Controllerで検索条件を処理し、Viewでフォームや結果を表示する

### Q3. `Ransack`と`ransack`の違いは？

`Ransack`はライブラリの名前

`ransack`は検索オブジェクトを作るメソッド

### Q4. `email_cont`はどういう意味？

メールアドレスに指定した文字を含むという検索条件

`email`がカラム名、`cont`が検索述語

### Q5. `search_form_for`は何をする？

Ransackの検索オブジェクトを使って、検索フォームを作るビューヘルパー

### Q6. `ransackable_attributes`は何をする？

検索や並べ替えに使うことを許可するカラムを指定する

### Q7. `@q`と`@users`はそれぞれ何に使う？

`@q`は検索条件の保持や検索フォームの作成に使う

`@users`は条件に合うユーザーの一覧表示に使う