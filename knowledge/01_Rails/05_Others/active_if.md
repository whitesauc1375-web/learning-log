

---
created: "2026-10-13"
tags:
  - 未理解
---

# active_if

## 📌 一言でいうと

**指定したパスが現在のコントローラーのパスと一致するとき、文字列 `'active'` を返す独自メソッド**

**分類：Viewを補助する表示用の処理（Helper向き）**

※ただし、**`active_if` は独自に付けたメソッド名**なので、別のアプリでも必ず同じ処理とは限らない

## 📖 仕組み・役割

```ruby
def active_if(path)
  path == controller_path ? 'active' : ''
end
```

引数 `path` と、現在のコントローラーを表す `controller_path` を比較する

|比較結果|戻り値|
|---|---|
|一致する|`'active'`|
|一致しない|`''`（空文字列）|

返された文字列をHTMLの `class` に使うことで、**現在表示しているメニューに目印を付けられる**

|コード|意味|
|---|---|
|`path`|メソッドに渡された比較対象|
|`controller_path`|現在のコントローラーのパス|
|`==`|左右の値が等しいか比較する|
|`条件 ? A : B`|条件が真ならA、偽ならBを返す三項演算子|

例えば、`BoardsController` の `controller_path` は `"boards"`  
`Admin::BoardsController` なら `"admin/boards"`

## 💻 コード例

```ruby
def active_if(path)
  path == controller_path ? 'active' : ''
end
```

`if` で書き直すと、次の処理と同じ

```ruby
def active_if(path)
  if path == controller_path
    'active'
  else
    ''
  end
end
```

**Viewでの使用例**

```ruby
<%= link_to "掲示板一覧", boards_path,
    class: active_if("boards") %>
```

現在のコントローラーが `BoardsController` なら、次のHTMLになる

```ruby
<a class="active" href="/boards">掲示板一覧</a>
```

`.active` に対応するCSSを用意すれば、文字色や背景色などを変えられる


## 🎯 使用場面

- 現在選択されているナビゲーションメニューを強調する
- サイドバーで、表示中の機能に目印を付ける
- View内の条件分岐を短くまとめる

## ⚠️ 注意点・よくある間違い
### ① `active_if` は今回独自に定義したメソッド

`def active_if(path)` の中に書かれた処理によって動作が決まる

### ② URLではなく、コントローラーのパスを比較する

```
active_if("boards")
```

この `path` という引数名でも、比較対象は `"/boards"` のようなURLのパスではない

```
# controller_path が "boards" の場合
active_if("boards")  # => "active"
active_if("/boards") # => ""
```

### ③ アクションの違いは判定しない

同じ `BoardsController` なら、`index`・`show`・`new` など、どのアクションでも `"boards"` に一致する

### ④ `'active'` を返すだけでは見た目は変わらない

HTMLの `class` に設定し、対応するCSSが適用されて初めて見た目が変わる


## 🔗 関連する知識

- [[Helper]]
- [[View]]
- [[controller_path]]
- [[三項演算子]]
- [[引数]]
- [[戻り値]]
- [[link_to]]
- [[CSS]]



## 💡 自分がつまずいたところ

<!-- 学習中に迷ったことを記入する -->


## ✅ 理解度チェック
- [ ] 自分の言葉で説明できる
- [ ] コードを読んで理解できる
- [ ] 自分でコードを書ける


## 🔄 復習メモ

### Q1. `active_if` は何を返す？

指定した値と `controller_path` が一致すれば `'active'`、一致しなければ空文字列を返す

### Q2. `条件 ? A : B` は何を意味する？

条件が真ならA、偽ならBを返す

### Q3. `BoardsController` の `controller_path` は？

`"boards"`

### Q4. 掲示板一覧と掲示板詳細を区別できる？

このコードでは区別できない  
コントローラーだけを比較し、アクションは比較していない