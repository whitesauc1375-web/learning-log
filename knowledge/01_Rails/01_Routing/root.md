

---
created: "2026-10-22"
tags:
  - 未理解
---

# root

## 📌 一言でいうと

Railsで、**トップページのURL `/` にアクセスしたとき、どのコントローラーのどのアクションで処理するかを決めるメソッド**

「サイトの入口に来た人を、どの処理へ案内するか」を設定するもの


## 📖 仕組み・役割

モデル・ビュー・コントローラーのいずれかに書くものではなく、`config/routes.rb` に書く

ルーティングは、受け取ったリクエストを適切なコントローラーのアクションへ振り分ける役割を持つ

例えば、次の設定を行う

```ruby
root "homes#index"
```

これは、`/` へのGETリクエストを `HomesController` の `index` アクションへ振り分ける設定

|記述|意味|
|---|---|
|`root`|トップページのルートを定義する|
|`homes`|処理を担当する `HomesController` を指定する|
|`#`|コントローラー名とアクション名を区切る|
|`index`|実行するアクションを指定する|

`root` を定義すると、トップページへのパスを表す `root_path` も使えるようになる

## 💻 コード例

### ① ルーティングを設定する

```ruby
# config/routes.rb
Rails.application.routes.draw do
  root "homes#index"
end
```

次の書き方でも同じ意味になる

```ruby
# config/routes.rb
Rails.application.routes.draw do
  root to: "homes#index"
end
```

どちらか一方を記述すればよい

### ② コントローラーにアクションを用意する

```ruby
# app/controllers/homes_controller.rb
class HomesController < ApplicationController
  def index
  end
end
```

### ③ 表示するビューを用意する

```ruby
<%# app/views/homes/index.html.erb %>
<h1>トップページ</h1>
<p>ようこそ</p>
```

この構成で `http://localhost:3000/` にアクセスすると、`HomesController` の `index` が実行され、対応するビューが表示される

### ④ トップページへのリンクを作る

```
<%= link_to "トップページへ", root_path %>
```

`root_path` は、この例では `/` を返すヘルパーメソッド


## 🎯 使用場面

- サイトのトップページを設定したいとき
- アプリの入口で掲示板一覧やホーム画面を表示したいとき
- ロゴや「ホーム」ボタンからトップページへ移動できるようにしたいとき

例えば、掲示板一覧をトップページにする場合は、次のように設定する

```ruby
# config/routes.rb
Rails.application.routes.draw do
  root "boards#index"
end
```

この場合は、`BoardsController` の `index` アクションでトップページのリクエストを処理する

## ⚠️ 注意点・よくある間違い

### `root` を書くだけでは画面は完成しない

`root` はリクエストの振り分け先を決める設定
指定したコントローラーやアクション、画面表示に必要なビューは別途用意する

### `index` 以外のアクションも指定できる

トップページのアクション名が `index` でなければならないわけではない
```ruby
root "homes#top"
```
この場合は、`HomesController` の `top` アクションが処理を担当する

### `/homes/index` に転送する設定ではない

```ruby
root "homes#index"
```
この設定でアクセスするURLは `/`
`homes#index` は処理の担当先を示す記述であり、アクセス先のURLではない

### `root` と `root_path` は役割が違う

|記述|役割|主な記述場所|
|---|---|---|
|`root`|トップページのルートを定義する|`config/routes.rb`|
|`root_path`|トップページへのパスを取得する|ビューやコントローラー|

### 通常の `root` はGETリクエストに対応する

フォーム送信などのPOSTリクエストを、この設定だけで受け付けるわけではない


## 🔗 関連する知識

- [[Routing]]
- [[routes.rb]]
- [[Controller]]
- [[View]]
- [[GET]]
- [[root_path]]
- [[link_to]]
- [[redirect_to]]
- [[resources]]




## 💡 自分がつまずいたところ

学習時に確認しておきたいポイント

- `root` は画面を作る処理ではなく、トップページのリクエストをどこで処理するか決める設定
- `"homes#index"` は「`HomesController` の `index`」という意味
- 文字列の中にある `#` は、Rubyのコメントを表しているわけではない
- URLの `/` と、処理の担当先である `homes#index` を区別する
- `root` を設定することと、`root_path` を使ってリンクを作ることを区別する


## ✅ 理解度チェック
- [ ] 自分の言葉で説明できる
- [ ] コードを読んで理解できる
- [ ] 自分でコードを書ける
- [ ] `root "homes#index"` の各部分の意味を説明できる
- [ ] `root` と `root_path` の違いを説明できる

## 🔄 復習メモ

### Q1. `root` は何をするメソッド？

トップページの `/` へのリクエストを、どのコントローラーのどのアクションで処理するか設定するメソッド

### Q2. `root` はどこに書く？

`config/routes.rb` の `Rails.application.routes.draw do ... end` の中に書く

### Q3. `root "boards#index"` はどういう意味？

`/` へのGETリクエストを、`BoardsController` の `index` アクションで処理するという意味

### Q4. `root_path` は何に使う？

トップページへのリンクやリダイレクト先を指定するときに使う

```
# コントローラー内でトップページへリダイレクトする例
redirect_to root_path
```

### Q5. `root` を設定すると、コントローラーやビューも自動作成される？

自動作成されないため、必要なコントローラーやビューは別途用意する