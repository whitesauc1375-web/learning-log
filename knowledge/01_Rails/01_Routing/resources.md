


---
created: "2026-10-10"
tags:
  - 未理解
---

# resources

## 📌 一言でいうと

`resources` は、**Railsでデータの登録・一覧表示・詳細表示・編集・削除に必要な7つのルーティングを、一度に自動作成してくれる仕組み**

例えば、掲示板アプリで以下のように記述すると、投稿に必要な7つのルーティングが自動生成される
```ruby
resources :boards
```

## 📖 仕組み・役割

Railsでは、ブラウザからアクセスされたURLを見て、どのコントローラーのどのアクションを実行するか判断する

このURLとアクションの対応関係を設定するのが「ルーティング」

`resources` を使うと、CRUD（作成・読み取り・更新・削除）を実現するためのルーティングがまとめて作成される


### ① resourcesで作成される7つのルーティング

`resources :boards` と記述すると、以下の7つが自動生成される

|HTTPメソッド|URL|アクション|役割|
|---|---|---|---|
|GET|`/boards`|index|投稿一覧|
|GET|`/boards/new`|new|新規投稿フォーム|
|POST|`/boards`|create|新規投稿を保存|
|GET|`/boards/:id`|show|投稿詳細|
|GET|`/boards/:id/edit`|edit|編集フォーム|
|PATCH/PUT|`/boards/:id`|update|投稿を更新|
|DELETE|`/boards/:id`|destroy|投稿を削除|

HTTPメソッドとは、サーバーに対して「何をしたいのか」を伝える種類のこと

- `GET`：データを取得する
- `POST`：データを送信して新規作成する
- `PATCH`：既存データの一部などを更新する
- `PUT`：既存データを更新する
- `DELETE`：データを削除する


### ② resourcesとCRUDの関係

|CRUD|意味|主なアクション|
|---|---|---|
|Create|作成|new、create|
|Read|読み取り|index、show|
|Update|更新|edit、update|
|Delete|削除|destroy

`resources` はCRUDに必要なルーティングを**まとめて定義するため、1つずつURLを設定する手間を減らせる**


### ③ MVCとの関係

![[Pasted image 20261010232158.png]]

重要：`resources` 自体はモデル・ビュー・コントローラーのどれでもなく、ルーティングの設定に分類される

また、`resources` を記述しただけで、コントローラーやビューが自動作成されるわけではない

## 💻 コード例

### ① 基本的な使い方

`config/routes.rb`
```ruby
Rails.application.routes.draw do
  resources :boards
end
```

これだけで掲示板の7つのルーティングを定義できる


### ② 必要なアクションだけ作成する
```ruby
resources :boards, only: [:index, :show, :new]
```

この場合、**一覧・詳細・新規作成フォームの3つのルーティングだけ**が作成される

反対に、特定のアクションを除外する場合は `except` を使う
```ruby
resources :boards, except: [:destroy]
```

削除以外の6つのルーティングが生成される


### ③ 投稿にコメントを紐づける
```ruby
resources :boards do
  resources :comments, only: [:create, :destroy]
end
```

掲示板の投稿に紐づくコメントの作成・削除ルーティングを設定する例

例えば `POST /boards/5/comments` は、IDが5の投稿に対するコメントの作成を意味する


### ④ 生成されたルーティングを確認する

ターミナルで以下を実行
```
bin/rails routes
```

`boards` に関するものだけ確認する場合
```
bin/rails routes -g board
```

登録されているHTTPメソッド、URL、アクション、ルーティングヘルパーなどを確認できる



## 🎯 使用場面

- 掲示板の投稿機能を実装するとき
- ユーザー登録・編集・削除機能を作るとき
- コメントの投稿・削除機能を作るとき
- 商品管理や記事管理などCRUDが必要な機能を作るとき

Railsでは、データを管理する機能を実装する際に頻繁に使用する


## ⚠️ 注意点・よくある間違い

① `resource` と `resources` は異なる
```ruby
resources :boards
```

複数の投稿を管理する場合に使用する
```ruby
resource :profile
```

ログインユーザー自身のプロフィールなど、原則として1つのリソースを扱う場合に使用する


② resourcesを書いても機能が完成するわけではない
```ruby
resources :boards
```

ルーティングが作られるだけなので、対応するコントローラーのアクションなども実装する必要がある


③ URLとHTTPメソッドの両方が重要

同じ `/boards` でも、

- `GET /boards` → index（一覧表示）
- `POST /boards` → create（新規作成）

というように、HTTPメソッドによって実行される処理が変わる


④ `resources` は原則として複数形で書く
```ruby
# 正しい
resources :boards

# 掲示板の複数リソースを定義する意図なら誤り
resources :board
```

モデル名は通常 `Board`、コントローラー名は `BoardsController` とする


## 🔗 関連する知識
- [[Controller]]
- [[HTTPメソッド]]
- [[RESTful]]




## 💡 自分がつまずいたところ

- `resources` を1行書くだけで、なぜ7つのルーティングが作成されるのか
- `resources` と `resource` の違いがわかりにくい
- `GET` と `POST` で同じURLを使う理由
- `resources` を定義すれば、コントローラーやビューも自動生成されると思っていた
- `index`・`show`・`new`・`create` など、7つのアクションの役割が混同しやすい
- `only` と `except` の使い分けがわかりにくい


## ✅ 理解度チェック
- [ ] 自分の言葉で説明できる
- [ ] コードを読んで理解できる
- [ ] 自分でコードを書ける


## 🔄 復習メモ

- `resources` は、RailsでCRUDに必要な7つのルーティングをまとめて定義する仕組
- 記述場所は `config/routes.rb`
- `resources :boards` と記述すると、`BoardsController` の7つのアクションに対応するルーティングが作成される
- `GET`・`POST`・`PATCH`・`PUT`・`DELETE` などのHTTPメソッドによって実行するアクションが決まる
- `only` は必要なアクションだけを指定する
- `except` は不要なアクションを除外する
- `resources` を書いただけでは、コントローラーやビューは作成されない
- `bin/rails routes` で設定したルーティングを確認できる