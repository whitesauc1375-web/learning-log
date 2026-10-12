- `yield` と `yield(:title)` の違い
    
- `content_for` と `yield` の関係
    
- `yield` 自体が文章を作っているわけではないこと
**分類：View（ビュー） / Layout（レイアウト）**

---
created: "2026-10-11"
tags:
  - 未理解
---

# yield

## 📌 一言でいうと

**指定した場所に、別の内容を差し込んで表示する仕組み。**

Railsでは、主に共通レイアウトに各ページの内容を表示するために使う。


## 📖 仕組み・役割

|書き方|役割|
|---|---|
|`yield`|各ページの本文を表示する|
|`yield(:title)`|`content_for` で保存したタイトルを表示する|
**覚え方：**`**yield**` **は「ここに内容を入れて！」という目印。**


## 💻 コード例

### ① yieldで本文を表示する

ファイル：`app/views/layouts/application.html.erb`

```erb
<body>
  <%= yield %>
</body>
```

ファイル：`app/views/boards/index.html.erb`

```erb
<h1>掲示板一覧</h1>
```

表示結果：

```html
<body>
  <h1>掲示板一覧</h1>
</body>
```

つまり、`yield` の場所に各ページの内容が入る。

### ② content_forと組み合わせる

ファイル：`app/views/boards/index.html.erb`

```erb
<% content_for(:title, "掲示板一覧") %>
```

ファイル：`app/views/layouts/application.html.erb`

```erb
<title><%= yield(:title) %></title>
```

表示結果：

```html
<title>掲示板一覧</title>
```

- `content_for` → 内容を保存する
- `yield(:title)` → 保存された内容を取り出す


## 🎯 使用場面

- ページごとに異なる本文を表示する
- ページごとにブラウザのタイトルを変更する
- 共通レイアウトの一部をページごとに変更する


## ⚠️ 注意点・よくある間違い

- `yield` と `yield(:title)` は役割が異なる
- `yield(:title)` で表示するには、基本的に対応する `content_for(:title, ...)` が必要
- HTMLに出力するときは `<%= yield %>` と書く
- Rubyの `yield` はブロックを呼び出す仕組みでもあり、Railsのレイアウトでの使い方とは区別する

## 🔗 関連する知識
- [[View]]
- [[Layout]]
- [[application.html.erb]]
- [[content_for]]
- [[render]]
- [[ERB]]
- [[i18n]]




## 💡 自分がつまずいたところ

- `yield` と `yield(:title)` の違い
- `content_for` と `yield` の関係
- `yield` 自体が文章を作っているわけではないこと


## ✅ 理解度チェック
- [ ] 自分の言葉で説明できる
- [ ] コードを読んで理解できる
- [ ] 自分でコードを書ける


## 🔄 復習メモ

### Q1. `yield` は何をする？

レイアウトの指定した場所に、各ページの本文を表示する。

### Q2. `yield(:title)` は何をする？

`content_for(:title, ...)` で保存した内容を表示する。

### Q3. `yield` はどこに書く？

Railsでは主に `app/views/layouts/application.html.erb` などのレイアウトファイルに書く。

### Q4. `content_for` と `yield` の違いは？

- `content_for` → 内容を保存
    
- `yield` → 内容を表示
    

---

**⭐ 最重要ポイント**

```erb
<%= yield %>
```

→ ページの本文を表示

```erb
<%= yield(:title) %>
```

→ 保存されたタイトルを表示

**「content_forで預けて、yieldで受け取る」** と覚える。