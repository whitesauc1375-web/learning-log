

---
created: "2026-11-08"
tags:
  - 未理解
---

# shallow

## 📌 一言でいうと

ルーティングで入れ子(ネスト)にしたリソースのURLを短くするオプション

子リソースのうち「親のIDが不要なアクション」だけ、親を省いたURLにしてくれる 

**分類: ルーティング(`config/routes.rb`)** 

View・Controller・Modelの3つのどれでもなく、リクエストを受けてどのControllerのどのアクションに渡すかを決める部分

役割としてはControllerの手前にあたるので、強いて分けるならController側の設定


## 📖 仕組み・役割

通常のネストでは、子リソースのすべてのURLに親のIDが入る

```
/articles/:article_id/comments/:id
```

ただ、コメントを1件表示・編集・削除するときは、コメントのIDがわかれば対象が決まる

親の記事IDまで毎回URLに入れるのは冗長になる

`shallow: true` を付けると、アクションごとに次のように振り分けられる

- 親が必要なアクション（`index` `new` `create`）→ 親のIDを含むURLのまま
- 親が不要なアクション（`show` `edit` `update` `destroy`）→ 子のIDだけのURLになる

|アクション|HTTPメソッド|URL|ルートヘルパー|
|---|---|---|---|
|index|GET|/articles/:article_id/comments|article_comments_path|
|new|GET|/articles/:article_id/comments/new|new_article_comment_path|
|create|POST|/articles/:article_id/comments|article_comments_path|
|show|GET|/comments/:id|comment_path|
|edit|GET|/comments/:id/edit|edit_comment_path|
|update|PATCH/PUT|/comments/:id|comment_path|
|destroy|DELETE|/comments/:id|comment_path|

親が必要なのは「どの親に属する子を作るか・一覧にするか」を決める必要があるアクションだけ

既存の子はIDだけで特定できるので、親のIDは省略できる、という考え方

## 💻 コード例

ruby

```ruby
# config/routes.rb

# 書き方①：子リソースにオプションとして付ける
Rails.application.routes.draw do
  resources :articles do
    resources :comments, shallow: true
  end
end

# 書き方②：親側に付けると、その中のネストすべてに適用される
Rails.application.routes.draw do
  resources :articles, shallow: true do
    resources :comments
  end
end

# 書き方③：shallow ブロックで囲む
Rails.application.routes.draw do
  shallow do
    resources :articles do
      resources :comments
    end
  end
end
```

Controller側では、アクションによって親の取り方が変わる

ruby

```ruby
class CommentsController < ApplicationController
  # 親のIDがURLに入るアクション
  def create
    @article = Article.find(params[:article_id])
    @comment = @article.comments.build(comment_params)
    # ...
  end

  # 子のIDだけがURLに入るアクション
  def destroy
    @comment = Comment.find(params[:id])
    @article = @comment.article # 親が必要ならモデルの関連から辿る
    # ...
  end
end
```

## 🎯 使用場面

- ネストが深くなってURLが長くなりすぎるとき
- 子リソースの編集・削除などを、子のIDだけで扱えれば十分なとき
- 記事とコメント、ブログと投稿のように、親子関係があるリソースを扱うとき

Railsガイドでも、ネストは1階層までにするのが望ましいとされている

`shallow` はその対策として使える

## ⚠️ 注意点・よくある間違い

- `shallow` はルーティングの設定なので、Modelの関連（`has_many` / `belongs_to`）自体は別に書く必要がある
- 親が不要なアクションでは `params[:article_id]` が来ない
    - 親が必要なら `@comment.article` のようにModelの関連から取得する
- ルートヘルパーの名前がアクションごとに変わる
    - `article_comment_path` のつもりで書いて、存在しないヘルパーのエラーになりやすい
- フォームでも注意が必要
    - 新規作成は `form_with model: [@article, @comment]`
    - 既存データの編集は `form_with model: @comment`
    - 既存のコメントに `[@article, @comment]` を渡すと、存在しないルートを探してエラーになる
- 実際にどんなルートができたかは `bin/rails routes` で確認できる

## 🔗 関連する知識

- [[routes]]
- [[resources]]
- [[nested resources]]
- [[Controller]]
- [[params]]
- [[has_many]]
- [[belongs_to]]
- [[form_with]]
- [[rails routes]]


## 💡 自分がつまずいたところ

学習時に確認しておきたいポイント

- どのアクションで親のIDが必要で、どのアクションで不要なのか
- ルートヘルパーの名前がアクションごとにどう変わるか
- 親が不要なアクションで、親の情報が欲しいときはどうするのか
- `form_with` に渡す引数が、新規作成と編集で変わる理由


## ✅ 理解度チェック
- [ ] 自分の言葉で説明できる
- [ ] コードを読んで理解できる
- [ ] 自分でコードを書ける


## 🔄 復習メモ

### Q1. `shallow: true` は何をするオプション？

ネストしたリソースのURLのうち、親のIDが不要なアクションの部分を短くするオプション

### Q2. `shallow: true` はどこに書く？

`config/routes.rb` に書く

View・Controller・Modelのいずれでもなく、ルーティングの設定

### Q3. `shallow: true` を付けると、親のIDが必要なまま残るアクションは？

`index` `new` `create`

どの親に属する子かを決める必要があるため

### Q4. `shallow: true` を付けると、親のIDが不要になるアクションは？

`show` `edit` `update` `destroy`

子のIDだけで対象を特定できるため

### Q5. 親が不要なアクションで親の情報が欲しいときは？

Modelの関連から辿る（例：`@comment.article`）