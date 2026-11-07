
## 📌 一言でいうと

ブラウザから送られてきたデータ（URL・フォームの入力値など）を受け取るためのメソッド

「リクエストに含まれていた情報の入れ物」として、Controllerの中で使う

**分類: Controller(アクションの中で使うメソッド)**

View・Modelではなく、リクエストの中身を受け取るControllerの役割

## 📖 仕組み・役割

`params` には、リクエストに含まれる次のデータがまとめて入っている

|入力元|例|取り出し方|
|---|---|---|
|URLのパス部分|`/articles/1`|`params[:id]` → `"1"`|
|クエリ文字列|`/articles?q=ruby`|`params[:q]` → `"ruby"`|
|フォームの入力値|`article[title]=Rails`|`params[:article][:title]` → `"Rails"`|

パス部分の `:id` のような名前は、`config/routes.rb` のルーティングで決まる

フォームの入力値は、`form_with` が作る `name` 属性（例：`article[title]`）がそのまま `params` の入れ子の構造になる

`form_with model: @article` で作ったフォームから送信されると、`params` はおおよそ次のような形になる

ruby

```ruby
{
  "article" => { "title" => "Rails入門", "body" => "本文" },
  "controller" => "articles",
  "action" => "create"
}
```

見た目はハッシュに近いが、実際には `ActionController::Parameters` というオブジェクト

そのため、Modelに渡す前に `permit` で許可したキーだけを取り出す必要がある（ストロングパラメータ）

`params[:id]` のように、キーはシンボルでも文字列でも同じように取り出せる

## 💻 コード例

ruby

```ruby
class ArticlesController < ApplicationController
  # GET /articles/1 → params[:id] は "1"
  def show
    @article = Article.find(params[:id])
  end

  # GET /articles?q=ruby → params[:q] は "ruby"
  def index
    @articles = Article.where(title: params[:q])
  end

  # POST /articles → フォームの入力値は params[:article] に入る
  def create
    @article = Article.new(article_params)

    if @article.save
      redirect_to @article
    else
      render :new, status: :unprocessable_entity
    end
  end

  private

  # 許可したキーだけを取り出す（ストロングパラメータ）
  def article_params
    params.require(:article).permit(:title, :body)
  end
end
```

## 🎯 使用場面

- URLに含まれるIDで、対象のレコードを探すとき（`Article.find(params[:id])`）
- 検索フォームの入力値や、絞り込みの条件を受け取るとき
- 新規作成・更新のフォームの入力値を、Modelに渡すとき
- ページ番号など、URLの `?` 以降の値を受け取るとき

実際に何が送られてきたかは、`log/development.log` の `Parameters:` の行で確認できる

## ⚠️ 注意点・よくある間違い

- `params` の値は、数字に見えても文字列として入っている
    - `params[:id]` は `1` ではなく `"1"`
    - `find` は文字列でも探せるが、数値として計算・比較したいときは `to_i` が必要
- `params` をそのまま `Article.new(params[:article])` に渡すと `ActiveModel::ForbiddenAttributesError` になる
    - `permit` で許可したキーだけを渡す
- `params[:article][:title]` と続けて書くと、`params[:article]` が `nil` のときに `NoMethodError` になる
    - `params.require(:article)` を使えば、キーがないときは `ActionController::ParameterMissing` になり、400エラーとして扱われる
- `permit` に書き忘れたキーは、エラーにならずに黙って無視される
    - フォームに入力した値が保存されないときは、まず `permit` を確認する
    - ログに `Unpermitted parameter` と出ていれば、これが原因
- 配列で送られてくる値は `permit(tag_ids: [])` のように、`[]` を付けて許可する
- Viewでも `params` は参照できるが、値を受け取って処理するのは基本的にControllerの仕事

## 🔗 関連する知識

- [[Controller]]
- [[params.require]]
- [[permit]]
- [[strong parameters]]
- [[form_with]]
- [[routes]]
- [[find]]
- [[redirect_to]]
- [[render]]

## 💡 自分がつまずいたところ

学習時に確認しておきたいポイント

- `params` はハッシュなのか、別のものなのか
- `require` と `permit` はそれぞれ何をしているのか
- フォームの `name` 属性が、`params` のどの形になるのか
- `params[:id]` が文字列になる理由と、困る場面
- フォームに入力した値が保存されないとき、何を確認するのか

## ✅ 理解度チェック

- [ ]  自分の言葉で説明できる
- [ ]  コードを読んで理解できる
- [ ]  自分でコードを書ける

## 🔄 復習メモ

### Q1. `params` は何のためのメソッド？

ブラウザから送られてきたデータ（URL・クエリ文字列・フォームの入力値）を受け取るためのメソッド

### Q2. `params` はどこで使う？

Controllerのアクションの中で使う

### Q3. `/articles/1` にアクセスしたとき、`1` はどう取り出す？

`params[:id]`

ただし値は数値ではなく、文字列の `"1"` として入っている

### Q4. フォームの `article[title]` はどう取り出す？

`params[:article][:title]`

### Q5. `params.require(:article).permit(:title, :body)` は何をしている？

`require` で `params[:article]` が存在することを必須にして、`permit` で `title` と `body` だけを許可して取り出している

### Q6. フォームに入力した値が保存されないときは何を確認する？

`permit` に、そのキーが書かれているかを確認する

書かれていないキーは、エラーにならずに無視されるため