

---
created: "2026-11-08"
tags:
  - 未理解
---

## 📌 一言でいうと

どのテンプレート（画面）や、どんな形式のデータをブラウザに返すかを指定するメソッド

別のURLへ移動せず、今のリクエストの中でそのまま画面を返す

**分類: Controller(アクションの中で使うメソッド)**

View・Modelではなく、返す内容を決めるControllerの役割

ただし、同じ名前の `render` はViewの中でも使われる

その場合は、部分テンプレート（パーシャル）を読み込む役割になる

## 📖 仕組み・役割

Controllerでの `render` は、「このテンプレートを使って、今のリクエストへの返事を作る」という指定

`render` を何も書かなかった場合は、アクション名と同じテンプレートが自動で使われる

例えば `articles#new` なら `app/views/articles/new.html.erb`

渡し方によって、返すものが変わる

|書き方|返すもの|
|---|---|
|`render :new`|同じControllerの `new` テンプレート|
|`render "articles/show"`|別のControllerのテンプレート|
|`render plain: "text"`|文字列だけ|
|`render json: @article`|JSON形式のデータ|
|`render :new, status: :unprocessable_entity`|ステータスコードを指定して画面を返す|

`redirect_to` との違いは次の通り

||render|redirect_to|
|---|---|---|
|新しいリクエスト|発生しない|発生する|
|ブラウザのURL|そのまま|移動先に変わる|
|インスタンス変数（`@article` など）|そのまま使える|引き継がれない|
|主な用途|失敗時のフォーム再表示、JSONの返却|成功後の移動|

Viewの中での `render` は、別ファイルに切り出した部分テンプレートを呼び出す

`render "form"` と書くと、`_form.html.erb` が読み込まれる

ファイル名の先頭の `_` は、`render` では書かない

## 💻 コード例

ruby

```ruby
class ArticlesController < ApplicationController
  def new
    @article = Article.new
    # render を書かなくても app/views/articles/new.html.erb が表示される
  end

  def create
    @article = Article.new(article_params)

    if @article.save
      redirect_to @article
    else
      # new.html.erb を表示する（new アクション自体は実行されない）
      render :new, status: :unprocessable_entity
    end
  end

  # JSONで返す場合
  def show
    article = Article.find(params[:id])
    render json: article
  end
end
```

Viewの中で部分テンプレートを読み込む例

erb

```erb
<%# app/views/articles/new.html.erb %>
<h1>新規作成</h1>

<%# app/views/articles/_form.html.erb を読み込む %>
<%= render "form", article: @article %>
```

## 🎯 使用場面

- 保存に失敗したとき、入力内容とエラーメッセージを残したままフォームを再表示するとき
- APIとして、JSONなどのデータを返すとき
- ステータスコードを指定して、エラー画面を返すとき
- Viewの中で、共通のフォームや一覧の部品を読み込むとき

## ⚠️ 注意点・よくある間違い

- `render :new` は `new` アクションを実行するのではなく、`new` のテンプレートを表示するだけ
    - テンプレートで使う `@` 付きの変数は、今のアクションの中で用意しておく必要がある
    - 例えば選択肢用のデータが必要なら、`create` の中でも用意し直す
- `render` を呼んでも、アクションの処理はそこで止まらない
    - 後ろに書いたコードも実行されるので、止めたいときは `return` を付ける
- 同じアクションの中で `render` と `redirect_to` の両方が実行されると `AbstractController::DoubleRenderError` になる
    - 条件分岐で、どちらか1つだけが実行されるようにする
- 失敗時に `render` で画面を返しても、ブラウザのURLは `/articles/new` ではなく `/articles` のまま
    - `create` アクションへ送信したURLが、そのまま表示されるため
- Turboを使うRails 7では、フォーム送信のあとに画面を再表示するときは `status: :unprocessable_entity` が必要
    - 付けないと、画面が更新されない
- 部分テンプレートのファイル名は `_form.html.erb` のように先頭に `_` が付くが、`render "form"` では `_` を書かない

## 🔗 関連する知識

- [[Controller]]
- [[redirect_to]]
- [[partial]]
- [[form_with]]
- [[params]]
- [[validation]]
- [[ERB]]
- [[HTTPステータスコード]]

## 💡 自分がつまずいたところ

学習時に確認しておきたいポイント

- `render` と `redirect_to` はどう使い分けるのか
- 保存に失敗したとき、`redirect_to` ではなく `render` を使う理由
- `render :new` を書いても `new` アクションが動かない理由
- Controllerの `render` と、Viewの `render` の違い
- `status: :unprocessable_entity` が必要な理由

## ✅ 理解度チェック

- [ ]  自分の言葉で説明できる
- [ ]  コードを読んで理解できる
- [ ]  自分でコードを書ける

## 🔄 復習メモ

### Q1. `render` は何をするメソッド？

どのテンプレートや、どんな形式のデータをブラウザに返すかを指定するメソッド

### Q2. `render` はどこに書く？

Controllerのアクションの中に書く

Viewの中に書くと、部分テンプレートを読み込む使い方になる

### Q3. `render` と `redirect_to` の違いは？

`render` は新しいリクエストが発生せず、そのまま画面を表示する

`redirect_to` は新しいリクエストが発生して、URLが変わる

### Q4. 保存に失敗したときに `redirect_to` ではなく `render` を使うのはなぜ？

`redirect_to` だと新しいリクエストになり、入力内容とエラーメッセージが消えてしまうため

`render` なら、`@article` をそのまま使ってフォームを再表示できる

### Q5. `render :new` は `new` アクションを実行する？

実行しない

`new` のテンプレートを表示するだけなので、必要な変数は今のアクションで用意しておく

### Q6. Viewで `render "form"` と書くと、どのファイルが読み込まれる？

`_form.html.erb`（部分テンプレート）