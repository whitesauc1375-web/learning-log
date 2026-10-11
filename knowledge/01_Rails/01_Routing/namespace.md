


---
created: "2026-10-11"
tags:
  - 未理解
---

# namespace

## 📌 一言でいうと

URLをグループ分けして、関連するルーティングを整理するための機能。

例えば、管理者専用のページを作る場合、

- `/admin/boards` → 管理者用の掲示板一覧
- `/admin/users` → 管理者用のユーザー一覧

このように、URLの先頭に共通の名前を付けて管理できる。

`namespace` を使うと、URLだけでなく、対応するコントローラーやルート名もまとめて整理できる。

## 📖 仕組み・役割

### ① namespaceの基本構造

```ruby
namespace :admin do
  resources :boards
end
```

| コード                 | 意味                    |
| ------------------- | --------------------- |
| `namespace`         | ルーティングをグループ化する        |
| `:admin`            | グループの名前（シンボル）         |
| `do ... end`        | グループに含めるルーティング        |
| `resources :boards` | 掲示板のRESTfulなルーティングを生成 |

つまり、
「管理者（admin）用の掲示板ルーティングをまとめて作って！」

とRailsに伝えている。

### ② namespaceを使うと3つの要素が変わる

```ruby
namespace :admin do
  resources :boards
end
```

このコードによって、主に以下の3つが変わる。

|項目|通常|namespace使用時|
|---|---|---|
|URL|`/boards`|`/admin/boards`|
|Controller|`BoardsController`|`Admin::BoardsController`|
|ルートヘルパー|`boards_path`|`admin_boards_path`|

![[Pasted image 20261011151003.png]]



### ③ 通常のresourcesとの違い

namespaceを使わない場合

```ruby
resources :boards
```

Railsは以下のルーティングを生成する。

```ruby
GET /boards
# => BoardsController#index
```

namespaceを使う場合

```ruby
namespace :admin do
  resources :boards
end
```

```ruby
GET /admin/boards
# => Admin::BoardsController#index
```

同じ掲示板一覧でも、一般ユーザー用と管理者用を分けて管理できる。

## 💻 コード例

### ① ルーティングを定義する

ファイル：`config/routes.rb`

```ruby
Rails.application.routes.draw do
  resources :boards

  namespace :admin do
    resources :boards
    resources :users
  end
end
```

これで、一般ユーザー用と管理者用のルーティングを分けられる。

### ② 管理者用コントローラーを作成する

ファイル：`app/controllers/admin/boards_controller.rb`

```ruby
class Admin::BoardsController < ApplicationController
  def index
    @boards = Board.all
  end
end
```

`Admin::BoardsController` は、管理者用の掲示板コントローラーを表している。

※ `Board` モデルが存在することを前提としている。

### ③ 管理者用Viewを作成する

ファイル：`app/views/admin/boards/index.html.erb`

```ruby
<h1>管理者用掲示板一覧</h1>

<% @boards.each do |board| %>
  <p><%= board.title %></p>
<% end %>
```

これで `/admin/boards` にアクセスした際に、管理者用の掲示板一覧を表示できる。

### ④ namespaceで生成されるルーティング

`resources :boards` を指定した場合の代表的なルーティングは次のとおり。

|HTTP|URL|ルートヘルパー|
|---|---|---|
|GET|`/admin/boards`|`admin_boards_path`|
|GET|`/admin/boards/new`|`new_admin_board_path`|
|POST|`/admin/boards`|`admin_boards_path`|
|GET|`/admin/boards/:id`|`admin_board_path(id)`|
|GET|`/admin/boards/:id/edit`|`edit_admin_board_path(id)`|
|PATCH|`/admin/boards/:id`|`admin_board_path(id)`|
|DELETE|`/admin/boards/:id`|`admin_board_path(id)`|

`resources` は通常の7つのCRUD系アクションに対応するルーティングを生成する。更新は `PUT` にも対応している。


## 🎯 使用場面

① 管理者ページを作成するとき

```ruby
namespace :admin do
  resources :users
  resources :boards
end
```

URLを `/admin/` から始まる形に統一できる。

② APIを作成するとき

```ruby
namespace :api do
  namespace :v1 do
    resources :boards
  end
end
```

生成されるURLは次のようになる。

```
/api/v1/boards
```

APIのバージョンごとに処理を整理できる。

③ 機能ごとにコントローラーを整理したいとき

例えば、管理者用機能が増えてきた際に、コントローラーを `admin` ディレクトリにまとめて管理できる。

## ⚠️ 注意点・よくある間違い

### ① namespaceを設定するだけではアクセス制限されない

```ruby
namespace :admin do
  resources :boards
end
```

これだけでは、管理者以外のユーザーがアクセスできないとは限らない。

`namespace` はルーティングを整理する機能であり、認証・認可の機能ではない。

管理者だけにアクセスを許可するには、コントローラー側でログイン状態や管理者権限のチェックが必要。

### ② Controllerの場所に注意する

```ruby
namespace :admin do
  resources :boards
end
```

この場合、Railsは基本的に次のコントローラーを探す。

```
app/controllers/admin/boards_controller.rb
```

通常の `app/controllers/boards_controller.rb` とは異なる。

### ③ ルートヘルパー名が変わる

通常のルーティングでは、

```
boards_path
```

namespaceを使うと、

```
admin_boards_path
```

となる。

`admin_` を付け忘れると、意図しないページへ移動したり、ルートが見つからなかったりする。

### ④ namespaceとscopeを混同しない

```ruby
namespace :admin do
  resources :boards
end
```

これは基本的に、URL・Controller・ルートヘルパーをまとめて変更する。

一方、

```ruby
scope :admin do
  resources :boards
end
```

こちらは、標準ではURLに `/admin` を付けるが、Controllerの名前空間やルートヘルパー名は変更しない。

|項目|`namespace :admin`|`scope :admin`|
|---|---|---|
|URL|`/admin/boards`|`/admin/boards`|
|Controller|`Admin::BoardsController`|`BoardsController`|
|ルートヘルパー|`admin_boards_path`|`boards_path`|

## 🔗 関連する知識
- [[Routing]]
- [[routes.rb]]
- [[resources]]
- [[resource]]
- [[scope]]
- [[Controller]]
- [[View]]
- [[MVC]]
- [[CRUD]]
- [[RESTful]]
- [[path]]
- [[before_action]]
- [[認証]]
- [[認可]]
- [[シンボル]]


## 💡 自分がつまずいたところ

- `namespace` はルーティングをグループ分けする機能であること
- URLだけでなくControllerやルートヘルパー名も変わること
- `Admin::BoardsController` の `::` は名前空間を表していること
- `namespace` を使っただけでは管理者専用のアクセス制限にはならないこと
- `namespace` と `scope` の違い


## ✅ 理解度チェック
- [ ] 自分の言葉で説明できる
- [ ] コードを読んで理解できる
- [ ] 自分でコードを書ける


## 🔄 復習メモ

### Q1. `namespace` は何をするメソッド？

ルーティングを名前空間でグループ分けし、URL・Controller・ルートヘルパーをまとめて整理するメソッド。

### Q2. `namespace` はどこに書く？

基本的に `config/routes.rb` に書く。

### Q3. `namespace :admin` はどういう意味？

`admin` という名前空間を作り、その中に管理者用のルーティングを定義するという意味。

### Q4. `namespace :admin` の中に `resources :boards` を書くと、一覧ページのURLは？

`/admin/boards`

### Q5. 管理者用の掲示板一覧に対応するControllerは？

`Admin::BoardsController` の `index` アクション。

### Q6. `admin_boards_path` は何を返す？

標準設定では `/admin/boards` というURLパスを返す。

### Q7. `namespace` を使えば管理者だけがアクセスできる？

いいえ。`namespace` はルーティングを整理する機能なので、アクセス制限には別途、認証・認可の仕組みが必要。

### Q8. `namespace` と `scope` の主な違いは？

- `namespace`：標準ではURL・Controllerの名前空間・ルートヘルパー名が変わる
    
- `scope :admin`：標準ではURLのパスだけが変わる
    

**最重要ポイント**

`namespace` は、「関連するルーティングを同じ名前空間にまとめ、URL・Controller・ルートヘルパーを整理する機能」。