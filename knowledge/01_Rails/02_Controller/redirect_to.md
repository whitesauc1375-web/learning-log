

---
created: "2026-11-08"
tags:
  - 未理解
---

# redirect_to

## 📌 一言でいうと

別のURLに移動するよう、ブラウザに指示を返すメソッド

画面を作って返すのではなく「次はこのURLにアクセスしてね」と伝えるだけ

**分類: Controller(アクションの中で使うメソッド)**

View・Modelではなく、リクエストを受けたあとの流れを決めるControllerの役割

## 📖 仕組み・役割

`redirect_to` が呼ばれると、次の流れで画面が表示される

1. ブラウザがリクエストを送る（例：`POST /articles`）
2. Controllerのアクション内で `redirect_to` が呼ばれる
3. サーバーは画面を返さず、ステータスコード302と移動先のURL（`Location` ヘッダ）だけを返す
4. ブラウザが移動先のURLに、あらためてGETリクエストを送る
5. 移動先のアクションが実行されて、画面が表示される

つまり、1回の操作でリクエストが2回発生する

`render` との違いは次の通り

||redirect_to|render|
|---|---|---|
|新しいリクエスト|発生する|発生しない|
|ブラウザのURL|移動先に変わる|そのまま|
|インスタンス変数（`@article` など）|引き継がれない|そのまま使える|
|主な用途|保存・削除の成功後の移動|失敗時のフォーム再表示|

引数には、移動先をいろいろな形で渡せる

|渡し方|移動先|
|---|---|
|`redirect_to articles_path`|パスヘルパーで指定したURL|
|`redirect_to @article`|そのレコードの詳細ページ（`article_url(@article)` と同じ）|
|`redirect_to "https://example.com", allow_other_host: true`|外部のURL|
|`redirect_back(fallback_location: root_path)`|直前のページ（戻れないときは `root_path`）|

## 💻 コード例

```ruby
class ArticlesController < ApplicationController
  def create
    @article = Article.new(article_params)

    if @article.save
      # 成功したら詳細ページへ移動する
      redirect_to @article, notice: "記事を作成しました"
    else
      # 失敗したらフォームを再表示する（移動はしない）
      render :new, status: :unprocessable_entity
    end
  end

  def destroy
    @article = Article.find(params[:id])
    @article.destroy
    redirect_to articles_path, notice: "記事を削除しました", status: :see_other
  end
end
```

`before_action` の中で使って、ログインしていないときにログイン画面へ移動させることもできる

ruby

```ruby
class ApplicationController < ActionController::Base
  before_action :require_login

  private

  def require_login
    redirect_to login_path unless logged_in?
  end
end
```

## 🎯 使用場面

- 作成・更新・削除が成功したあとに、一覧や詳細ページへ移動するとき
- ログインしていないユーザーをログイン画面へ移動させるとき
- 権限がないユーザーをトップページへ戻すとき
- 直前のページに戻したいとき（`redirect_back`）

保存成功後にリダイレクトするのは、画面を再読み込みしたときに同じ保存処理がもう一度実行されるのを防ぐため

この流れはPRGパターン（Post / Redirect / Get）と呼ばれる



## ⚠️ 注意点・よくある間違い

- `redirect_to` を呼んでも、アクションの処理はそこで止まらない
    - 後ろに書いたコードも実行されるので、止めたいときは `return` を付ける
- 同じアクションの中で `render` と `redirect_to` の両方が実行されると `AbstractController::DoubleRenderError` になる
    - 条件分岐で、どちらか1つだけが実行されるようにする
- リダイレクト先ではインスタンス変数が引き継がれない
    - 新しいリクエストとして処理されるため
    - メッセージを渡したいときは `notice:` `alert:`（フラッシュ）を使う
- バリデーション失敗のときに `redirect_to` を使うと、エラーメッセージが消える
    - 失敗時のフォーム再表示は `render` を使う
    - Rails 7では `status: :unprocessable_entity` も付ける
- `redirect_to :back` はRails 5.1で廃止された
    - 代わりに `redirect_back(fallback_location: ...)` を使う
- 外部サイトへのリダイレクトは、`allow_other_host: true` を付けないとエラーになる設定がある（Rails 7.0以降の設定）
    - ユーザーが入力したURLをそのまま渡すのは危険（オープンリダイレクトの脆弱性）
- Turboを使っている場合、DELETEのあとのリダイレクトには `status: :see_other` を付ける
    - 付けないと、リダイレクト先にもDELETEが送られてエラーになることがある

## 🔗 関連する知識

- [[render]]
- [[Controller]]
- [[before_action]]
- [[require_login]]
- [[flash]]
- [[notice]]
- [[alert]]
- [[redirect_back]]
- [[routes]]
- [[HTTPステータスコード]]

## 💡 自分がつまずいたところ

学習時に確認しておきたいポイント

- `redirect_to` と `render` はどう使い分けるのか
- 成功したときは `redirect_to`、失敗したときは `render` を使う理由
- リダイレクト先で `@article` が使えない理由
- `notice:` はどこに表示されるのか
- `redirect_to @article` と `redirect_to article_path(@article)` は何が同じで何が違うのか

## ✅ 理解度チェック

- [ ]  自分の言葉で説明できる
- [ ]  コードを読んで理解できる
- [ ]  自分でコードを書ける

## 🔄 復習メモ

### Q1. `redirect_to` は何をするメソッド？

別のURLに移動するよう、ブラウザに指示を返すメソッド

### Q2. `redirect_to` はどこに書く？

Controllerのアクション（または `before_action` で呼ぶメソッド）の中に書く

### Q3. `redirect_to` と `render` の違いは？

`redirect_to` は新しいリクエストが発生してURLが変わる

`render` は新しいリクエストが発生せず、そのまま画面を表示する

### Q4. 保存に成功したときと失敗したとき、それぞれ何を使う？

成功したときは `redirect_to`

失敗したときは `render`（入力内容とエラーメッセージを残したまま、フォームを再表示するため）

### Q5. `redirect_to` のあとにインスタンス変数が使えないのはなぜ？

リダイレクト先は新しいリクエストとして処理されるため

### Q6. 同じアクションで `render` と `redirect_to` を両方呼ぶとどうなる？

`AbstractController::DoubleRenderError` になる