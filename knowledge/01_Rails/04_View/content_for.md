


---
created: "2026-10-11"
tags:
  - 未理解
---

# content_for(~~~)

## 📌 一言でいうと

**Webページごとに異なるタイトルを設定するために使うメソッド。**

`content_for` は、ビューで指定した内容を一時的に保存し、レイアウトファイルの好きな場所で表示できる仕組み。

例えば、掲示板の一覧ページでは「掲示板一覧」、詳細ページでは「掲示板詳細」というように、ページごとにブラウザのタイトルを変更できる。

## 📖 仕組み・役割

### ① `content_for` の基本構造

```
<% content_for(:title, "掲示板一覧") %>
```

|コード|意味|
|---|---|
|`content_for`|指定した内容を保存するRailsのメソッド|
|`:title`|保存する場所を識別する名前（シンボル）|
|`"掲示板一覧"`|保存する文字列|

つまり、

「`:title` という名前で『掲示板一覧』を保存しておいて！」

とRailsに伝えている。

### ② `yield(:title)` で保存した内容を取り出す

`content_for` で保存しただけでは、タイトルは表示されない。

レイアウトファイルに `yield(:title)` を書くことで、保存した内容を取り出せる。
![[Pasted image 20261011133218.png]]

### ③ `content_for` と `yield` の違い

- `content_for`：内容を保存する側
- `yield`：保存された内容を取り出す側


## 💻 コード例

### ① ビューファイルに書く

ファイル：`app/views/boards/index.html.erb`

```ruby
<% content_for(:title, "掲示板一覧") %>

<h1>掲示板一覧</h1>
```

`"掲示板一覧"` を `:title` という名前で保存する。

### ② 共通レイアウトに書く

ファイル：`app/views/layouts/application.html.erb`

```ruby
<!DOCTYPE html>
<html>
  <head>
    <title><%= yield(:title) %></title>
  </head>

  <body>
    <%= yield %>
  </body>
</html>
```

- `yield(:title)`：保存したページタイトルを取り出す
    
- `yield`：各ページの本文を表示する
    

### ③ 実際のブラウザ表示

```ruby
<title>掲示板一覧</title>
```

ブラウザのタブに「掲示板一覧」と表示される。

### ④ i18nと組み合わせる場合

RUNTEQの課題では、次のような書き方もよく使われる。

```ruby
<% content_for(:title, t('.title')) %>
```

この場合、

- `content_for`：タイトルを保存する
    
- `:title`：保存先を識別する名前
    
- `t('.title')`：翻訳ファイルからタイトルを取得する
    

例えば、`config/locales/ja.yml` に以下の設定がある場合、

```ruby
ja:
  boards:
    index:
      title: "掲示板一覧"
```

`app/views/boards/index.html.erb` の `t('.title')` は、対応する「掲示板一覧」という文字列を取得する。

その結果、次のコードと同じ内容を保存できる。

```ruby
<% content_for(:title, "掲示板一覧") %>
```



## 🎯 使用場面

① ページごとにブラウザのタイトルを変更したいとき

- 掲示板一覧 → 「掲示板一覧」
- 掲示板詳細 → 「掲示板詳細」
- ユーザー登録 → 「新規登録」
- ログイン → 「ログイン」


② 共通レイアウトの一部をページごとに変更したいとき

`content_for` はタイトル専用ではない。

例えば、次のような指定もできる。

```ruby
<% content_for(:sidebar, "おすすめ記事") %>
```

レイアウトで `yield(:sidebar)` を使えば、その内容を表示できる。


## ⚠️ 注意点・よくある間違い

### ① `content_for` だけではタイトルは表示されない

```ruby
<% content_for(:title, "掲示板一覧") %>
```

保存した内容を表示するには、レイアウト側に `yield(:title)` が必要。

### ② `<% %>` と `<%= %>` を混同しない

```ruby
<% content_for(:title, "掲示板一覧") %>
```

`<% %>` はRubyの処理を実行するが、その戻り値を直接HTMLに出力しない。

```ruby
<%= yield(:title) %>
```

`<%= %>` はRubyの処理結果をHTMLに出力する。

### ③ `:title` と `"title"` の役割を区別する

```ruby
<% content_for(:title, "掲示板一覧") %>
```

- `:title` はシンボルで、保存する内容を識別する名前
    
- `"掲示板一覧"` は文字列で、実際に保存する内容
    

### ④ `yield` だけではタイトルを取得できない

```ruby
<%= yield %>
```

これは通常、ページの本文を表示する。

```ruby
<%= yield(:title) %>
```

こちらは `:title` に保存された内容を取り出す。

### ⑤ タイトルが設定されていないページに注意

`content_for(:title, ...)` が設定されていないページでは、`yield(:title)` は通常、空の内容になる。

初期値を設けたい場合は、次のように書ける。

```ruby
<title>
  <%= content_for?(:title) ? yield(:title) : "MyApp" %>
</title>
```

`content_for?` は指定した名前に内容が設定されているか確認するメソッド。

## 🔗 関連する知識

- [[View]]    
- [[Layout]]
- [[application.html.erb]]
- [[yield]]
- [[content_for]]
- [[ERB]]
- [[i18n]]
- [[t]]
- [[シンボル]]
- [[HTML]]
- [[render]]



## 💡 自分がつまずいたところ

- `content_for` と `yield` の役割の違い
- なぜ `content_for` は `<% %>` で、`yield(:title)` は `<%= %>` なのか
- `:title` は画面に表示する文字ではなく、保存内容の識別名であること
- `t('.title')` と `content_for(:title, ...)` の関係


## ✅ 理解度チェック
- [ ] 自分の言葉で説明できる
- [ ] コードを読んで理解できる
- [ ] 自分でコードを書ける


## 🔄 復習メモ

### Q1. `content_for` は何をするメソッド？

ビューで指定した内容を名前付きで保存し、レイアウトなどの別の場所で表示できるようにするメソッド。

### Q2. `content_for` はどこに書く？

基本的にViewファイル（`.html.erb`）に書く。

### Q3. `:title` は何を表している？

保存する内容を識別するシンボル。

### Q4. `yield(:title)` の役割は？

`content_for(:title, ...)` で保存した内容を取り出して表示する。

### Q5. `content_for(:title, t('.title'))` は何をしている？

i18nの翻訳ファイルからタイトルを取得し、`:title` という名前で保存している。

### Q6. `content_for` と `render` の違いは？

- `content_for`：内容を保存して、別の場所で利用する
    
- `render`：テンプレートや部分テンプレートを描画する