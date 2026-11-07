

---
created: "2026-11-07"
tags:
  - 未理解
---

# nested routes

## 📌 一言でいうと

**「どの掲示板のコメントか」のような親子関係を、URLで表現するルーティング**

たとえば `/boards/1/comments/5` は「掲示板IDが1の掲示板にある、コメントIDが5のコメント」を表す

**分類：ルーティング**  
主な記述先は `config/routes.rb` で、モデル・ビュー・コントローラーのいずれにも直接分類されない設定

## 📖 仕組み・役割

`nested` は「入れ子になった」という意味

Railsでは、親の `resources` のブロック内に子の `resources` を書くことで、親子関係を持つルートを定義できる

```ruby
resources :boards do
  resources :comments
end
```

この場合、親が掲示板、子がコメントになる

子のルートには親を特定する `:board_id` が含まれ、個別のコメントを扱うルートにはコメント自身を特定する `:id` も含まれる

|HTTPメソッド|URL|呼び出されるアクション|役割|
|---|---|---|---|
|GET|`/boards/:board_id/comments`|`comments#index`|掲示板のコメント一覧|
|GET|`/boards/:board_id/comments/new`|`comments#new`|コメントの新規入力画面|
|POST|`/boards/:board_id/comments`|`comments#create`|コメントの作成|
|GET|`/boards/:board_id/comments/:id`|`comments#show`|コメントの詳細|
|GET|`/boards/:board_id/comments/:id/edit`|`comments#edit`|コメントの編集画面|
|PATCH / PUT|`/boards/:board_id/comments/:id`|`comments#update`|コメントの更新|
|DELETE|`/boards/:board_id/comments/:id`|`comments#destroy`|コメントの削除|

**URLが同じでも、HTTPメソッドによって呼び出されるアクションが変わる**

これらは `CommentsController` に対応し、親側の `BoardsController` にコメントの処理を書くわけではない

## 💻 コード例

**① ルーティングで親子関係を表す**

```ruby
# config/routes.rb
Rails.application.routes.draw do
  resources :boards do
    resources :comments, only: [:index, :show]
  end
end
```

`only:` は、作成するルートを指定したアクションに限定するオプション

この例では、コメントの一覧と詳細のルートだけを定義する

**② モデルでデータの関連付けを定義する**

以下は、`comments` テーブルに `board_id` カラムがある前提

```ruby
# app/models/board.rb
class Board < ApplicationRecord
  has_many :comments
end
```

```ruby
# app/models/comment.rb
class Comment < ApplicationRecord
  belongs_to :board
end
```

`has_many :comments` は、掲示板から関連するコメントを扱うための関連付け

`belongs_to :board` は、コメントから所属先の掲示板を扱うための関連付け

**③ コントローラーで親を取得し、その親のコメントを探す**

```ruby
# app/controllers/comments_controller.rb
class CommentsController < ApplicationController
  def index
    @board = Board.find(params[:board_id])
    @comments = @board.comments
  end

  def show
    @board = Board.find(params[:board_id])
    @comment = @board.comments.find(params[:id])
  end
end
```

`GET /boards/1/comments/5` のリクエストでは、URL内の値を次のように受け取る

```
params[:board_id] # => "1"
params[:id]       # => "5"
```

処理の意味は次のとおり

|コード|意味|
|---|---|
|`Board.find(params[:board_id])`|URLで指定された掲示板を取得|
|`@board.comments`|その掲示板に所属するコメントを取得するための関連|
|`@board.comments.find(params[:id])`|その掲示板のコメントに限定して、指定IDのコメントを取得|

**④ ビューで親と子を渡してリンクを作る**

```ruby
<%# app/views/comments/index.html.erb %>
<% @comments.each do |comment| %>
  <%= link_to "コメントを見る", board_comment_path(@board, comment) %>
<% end %>
```

`board_comment_path(@board, comment)` は、親の掲示板と子のコメントからパスを作るヘルパーメソッド

```ruby
# @board.id が1、comment.id が5の場合
board_comment_path(@board, comment)
# => "/boards/1/comments/5"
```


## 🎯 使用場面

**特定の親に所属するデータを扱うとき**

|親|子|URLの例|
|---|---|---|
|掲示板|コメント|`/boards/1/comments`|
|プロジェクト|タスク|`/projects/2/tasks`|
|記事|レビュー|`/articles/3/reviews`|

掲示板とコメントなら「コメント全体の一覧」ではなく「ある掲示板に付いたコメントの一覧」を扱う場面に適している

## ⚠️ 注意点・よくある間違い

**① ルートをネストしても、モデルの関連付けは作られない**

それぞれ役割が異なる

|設定|役割|
|---|---|
|ネストしたルーティング|URLと処理の対応、URL上の親子関係を定義|
|`has_many` / `belongs_to`|モデル同士の関連付けを定義|
|`board_id` カラム|コメントの所属先のIDを保存|

`routes.rb` に書くだけで、カラムやモデル、コントローラーが自動作成されるわけではない

**② 親のIDと子のIDを取り違えない**

コメントの詳細ルート `/boards/:board_id/comments/:id` では、次の対応になる

```
params[:board_id] # 掲示板のID
params[:id]       # コメントのID
```

ただし、掲示板自身の詳細ルート `/boards/:id` では、`params[:id]` は掲示板のIDになる

**`params[:id]` の意味は、どのルートで処理しているかによって変わる**

**③ URLの親子関係は、自動で検証されない**

```ruby
# 全コメントから探す
@comment = Comment.find(params[:id])
```

これだけでは、取得したコメントがURLで指定された掲示板に所属するかを確認できない

```ruby
# 指定された掲示板のコメントから探す
@comment = @board.comments.find(params[:id])
```

こちらは検索対象をその掲示板のコメントに限定するため、別の掲示板のコメントは取得されない

ただし、ユーザーが閲覧・編集できるかという権限の確認は別途必要

**④ ネストを深くしすぎない**

親・子・孫と重ねると、URLやヘルパーに必要な引数が増えて複雑になる

Rails公式ガイドでは、ネストは原則1段までが推奨されている

## 🔗 関連する知識

- [[routing]]
- [[resources]]
- [[params]]
- [[Controller]]
- [[Model]]
- [[has_many]]
- [[belongs_to]]
- [[find]]
- [[link_to]]
- [[shallow routes]]



## 💡 自分がつまずいたところ

- `resources :boards` の内側に書いても、コメントの処理先は `CommentsController`
- コメントの詳細ルートでは、`board_id` が親のID、`id` が子のID
- `board_comment_path` には、基本的に親・子の順番で引数を渡す
- ルーティングのネストと、モデルの関連付けは別の設定
- URLに親のIDがあっても、所属関係の確認はコントローラー側の取得処理などで行う


## ✅ 理解度チェック
- [ ] 自分の言葉で説明できる
- [ ] コードを読んで理解できる
- [ ] 自分でコードを書ける
- [ ] `params[:board_id]` と `params[:id]` の違いを説明できる
- [ ] 親に所属する子だけを取得するコードを書ける
- [ ] ルーティングとモデルの関連付けの違いを説明できる


## 🔄 復習メモ

### Q1. nested routes は何をする仕組み？

掲示板とコメントのような親子関係をURLで表現する仕組み

### Q2. nested routes はどこに書く？

`config/routes.rb` に書く

### Q3. `/boards/1/comments/5` の `1` と `5` は何？

`1` は掲示板のID、`5` はコメントのID

コメントの詳細アクションでは、それぞれ `params[:board_id]` と `params[:id]` で受け取る

### Q4. なぜ `@board.comments.find(params[:id])` と書く？

指定された掲示板に所属するコメントだけに検索対象を限定するため

### Q5. ネストすれば `has_many` や `belongs_to` は不要になる？

不要にはならない

ネストはルーティングの設定で、モデルの関連付けは別途定義する必要がある

### Q6. `board_comments_path(@board)` と `board_comment_path(@board, comment)` の違いは？

`board_comments_path(@board)` は、その掲示板のコメント全体を対象とするパス

`board_comment_path(@board, comment)` は、その掲示板の特定のコメントを対象とするパス

```ruby
board_comments_path(@board)
# => "/boards/1/comments"

board_comment_path(@board, comment)
# => "/boards/1/comments/5"
```