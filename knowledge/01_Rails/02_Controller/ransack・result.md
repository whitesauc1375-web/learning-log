

---
created: "2026-10-15"
tags:
  - 未理解
---

# ransack・result

## 📌 一言でいうと

`ransack`は検索条件をまとめるメソッド
`result`はその条件を反映したデータ取得用のオブジェクトを返すメソッド

- `ransack` → どんな条件で検索するかを準備する
- `result` → その条件に合うレコードを取得できる形にする

**分類：今回のコードを書く場所はController（コントローラー）**

どちらも検索機能を作るgem「Ransack」が提供するメソッドで、Rails標準のメソッドではない


## 📖 仕組み・役割

```ruby
def index
  @q = User.ransack(params[:q])
  @users = @q.result
end
```

このコードは、検索フォームから受け取った条件を使って、一覧に表示するユーザーを絞り込む処理

### ① `params[:q]`で検索条件を受け取る

`params`は、ブラウザから送られてきた値などを受け取るためのもの

`params[:q]`には、Ransackの検索条件が入る

たとえば、メールアドレスに「example」を含むユーザーを探す場合、次のような値を受け取る

```ruby
{ "email_cont" => "example" }
```

|部分|意味|
|---|---|
|`email`|検索対象のカラム|
|`cont`|指定した文字を含むという検索条件|
|`"example"`|検索する文字|

`q`はRansackが標準で使う検索パラメータの名前

### ② `ransack`で検索オブジェクトを作る

```ruby
@q = User.ransack(params[:q])
```

`User`を検索対象として、受け取った検索条件をもとに**検索オブジェクト**を作る

検索オブジェクトとは、検索条件や並び順などの情報を保持するもの

ここでの`@q`は、検索で見つかったユーザー一覧ではない

**「Userをどんな条件で検索するか」という情報を持ったもの**

また、ビューの`search_form_for`に渡して、検索フォームを作るためにも使う

### ③ `result`で検索条件を反映する

```ruby
@users = @q.result
```

`@q`が持っている条件を反映した`ActiveRecord::Relation`を返し、`@users`に代入する

`ActiveRecord::Relation`とは、**取得対象や絞り込み条件などを保持し、必要なときにデータベースからレコードを取得できるオブジェクト**

初心者向けには「検索条件に合うユーザー一覧を扱うためのもの」と考えると分かりやすい

公式の基本例でも、`ransack`で検索オブジェクトを作り、`result`を使って一覧表示用の変数に代入する構成になっている [Ransack](https://activerecord-hackery.github.io/ransack/getting-started/simple-mode/?utm_source=chatgpt.com)

### `@q`と`@users`の違い

|変数|入っているもの|主な用途|
|---|---|---|
|`@q`|`Ransack::Search`という検索オブジェクト|検索条件の保持、検索フォームの作成|
|`@users`|検索条件を反映した`ActiveRecord::Relation`|条件に合うユーザーの一覧表示|

## 💻 コード例

メールアドレスに指定した文字を含むユーザーを検索する例

検索対象の`email`カラムが存在し、Ransackで検索を許可していることが前提

```ruby
# app/controllers/users_controller.rb
class UsersController < ApplicationController
  def index
    # 検索フォームから受け取った条件で検索オブジェクトを作る
    @q = User.ransack(params[:q])

    # 検索条件を反映したRelationを代入する
    @users = @q.result
  end
end
```

検索フォームと一覧表示の例

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

フォームを使わず、検索条件を直接指定することもできる

```ruby
# メールアドレスに「example」を含むユーザーを検索する準備
@q = User.ransack(email_cont: "example")

# 条件を反映したRelationを取得
@users = @q.result
```


## 🎯 使用場面

- ユーザー一覧を名前やメールアドレスで検索するとき
- 掲示板一覧をタイトルに含まれる文字で絞り込むとき
- 商品一覧を価格などの条件で絞り込むとき
- 検索フォームと検索結果の一覧を組み合わせるとき

**検索フォームでは`@q`、結果の一覧表示では`@users`を使う**と整理すると覚えやすい

## ⚠️ 注意点・よくある間違い

### `ransack`だけではユーザー一覧にならない

```ruby
@q = User.ransack(params[:q])
```

この段階で得られるものは、検索条件を持った検索オブジェクト

一覧として扱うためには、`result`でRelationを取得する

```ruby
@users = @q.result
```

### `result`を呼んだ瞬間に、必ずデータベースへ問い合わせるわけではない

Active Recordには、データが必要になるまで取得を待つ**遅延読み込み**という仕組みがある

```ruby
# 通常、この時点ではレコードの読み込みはまだ行われない
@users = @q.result

# eachでレコードを使う段階で読み込まれる
@users.each do |user|
  puts user.email
end
```

そのため、`result`は「その場で必ず検索を実行するメソッド」と覚えるより、**「検索条件を反映したRelationを返すメソッド」**と覚える方が正確

### 検索条件がなければ、通常は絞り込まれない

```ruby
@q = User.ransack(nil)
@users = @q.result
```

今回のように`User`を対象とし、ほかの絞り込み設定がなければ、全ユーザーが取得対象になる

検索条件がないことと、検索結果が0件になることは別

### 検索できるカラムの許可設定が必要

Ransack 4.0以降では、検索や並べ替えの対象を明示的に許可する必要がある

カラムの許可設定には、モデルの`ransackable_attributes`を使う

これはControllerで検索処理を書くこととは別の、Model側の設定 [Ransack](https://activerecord-hackery.github.io/ransack/getting-started/simple-mode/?utm_source=chatgpt.com)

### `@q`という変数名自体に特別な機能はない

`@q`は慣例的によく使われる名前

別の名前にも変更できるが、コントローラーとビューで同じ名前を使う必要がある

```ruby
@search = User.ransack(params[:q])
@users = @search.result
```

`@q`という変数名と、検索条件を受け取る`params[:q]`のキーは別のもの

## 🔗 関連する知識

- [[Ransack]]
- [[params]]
- [[Controller]]
- [[Model]]
- [[View]]
- [[ActiveRecord::Relation]]
- [[search_form_for]]
- [[ransackable_attributes]]


## 💡 自分がつまずいたところ

学習時に確認しておきたいポイント

- `@q`に検索結果のユーザーが入っていると思いやすいが、入っているのは検索条件を保持するオブジェクト
- `result`が返すものは通常の配列ではなく、`ActiveRecord::Relation`
- `ransack`と`result`はControllerに書いていても、Controller専用のメソッドではない
- `@q`は検索フォーム用、`@users`は一覧表示用と役割を分けると整理しやすい


## ✅ 理解度チェック
- [ ] 自分の言葉で説明できる
- [ ] コードを読んで理解できる
- [ ] 自分でコードを書ける
- [ ] `@q`と`@users`の違いを説明できる
- [ ] `ransack`と`result`の役割の違いを説明できる

## 🔄 復習メモ

### Q1. `ransack`は何をするメソッド？

渡された検索条件をもとに、Ransackの検索オブジェクトを作るメソッド

### Q2. `result`は何をするメソッド？

検索オブジェクトが持っている条件を反映した`ActiveRecord::Relation`を返すメソッド

### Q3. `params[:q]`には何が入る？

検索フォームから送られてきた検索条件

たとえば、メールアドレスに「example」を含むという条件なら、`email_cont: "example"`のような情報が入る

### Q4. `@q`と`@users`の違いは？

`@q`は検索条件を保持する検索オブジェクト

`@users`は条件に合うユーザーを取得して扱うためのRelation

### Q5. このコードはどこに書く？

今回のような一覧検索の処理はControllerの`index`アクションに書く

検索を許可するカラムの設定はModel、検索フォームや結果の表示はViewが担当する

### Q6. `result`を呼ぶと、その場で必ずデータベースから取得する？

必ずではない

通常は、`each`などで実際にレコードが必要になった段階で読み込む