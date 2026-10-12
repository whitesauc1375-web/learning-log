

---
created: "2026-10-11"
tags:
  - 未理解
---

# save・save！

**分類：Model（モデル） / Active Record**

## 📌 一言でいうと

どちらも**データベースにデータを保存するメソッド**。

違いは、**保存に失敗したときの動作**。

- `save` → 通常、バリデーションに失敗すると `false` を返す
- `save!` → バリデーションに失敗すると例外（エラー）を発生させる

## 📖 仕組み・役割

|比較|`save`|`save!`|
|---|---|---|
|保存成功|`true`|`true`|
|バリデーション失敗|`false`|例外発生|
|主な用途|フォームの登録・更新|失敗を例外として扱う処理|
|バリデーション|実行する|実行する|

## 💻 コード例
### ① saveの場合

```ruby
board = Board.new(title: "")

board.save
# => false
```

タイトルが必須の場合、保存に失敗して `false` が返る。

### ② save!の場合

```ruby
board = Board.new(title: "")

board.save!
# => ActiveRecord::RecordInvalid
```

保存に失敗すると例外が発生する。

### ③ Controllerでの使用例

```ruby
def create
  @board = Board.new(board_params)

  if @board.save
    redirect_to boards_path
  else
    render :new, status: :unprocessable_entity
  end
end
```

- 保存成功 → 掲示板一覧へ移動
- 保存失敗 → 新規投稿画面を再表示
    

※ 例ではModelに `validates :title, presence: true` が設定されている前提。



## 🎯 使用場面

**save**

- ユーザー登録
- 掲示板の投稿
- プロフィール更新

**save!**

- 初期データの登録
- テストデータの作成
- 保存失敗を例外として検知したい処理

## ⚠️ 注意点・よくある間違い

- `!` は「必ず保存できる」という意味ではない
- `save!` でもバリデーションは実行される
- `save` でもデータベースの問題などで例外が発生する
- `save` と `save!` は新規作成・更新の両方に使える


## 🔗 関連する知識

- [[疑問符（?）と感嘆符（!）]    
- [[Model]]
- [[ActiveRecord]]
- [[validation]]
- [[validates]]
- [[create・create!]]
- [[update・update!]]
- [[errors.full_messages]]


## 💡 自分がつまずいたところ

- `save` と `save!` の違い
- `!` は強制保存という意味ではない
- 保存失敗時に `false` が返る場合と例外が発生する場合がある


## ✅ 理解度チェック
- [ ] 自分の言葉で説明できる
- [ ] コードを読んで理解できる
- [ ] 自分でコードを書ける


## 🔄 復習メモ

### Q1. `save` は何をするメソッド？

データをDBに保存する。バリデーション失敗時は通常 `false` を返す。

### Q2. `save!` との違いは？

バリデーション失敗時に例外を発生させる。

### Q3. `save!` の `!` は必ず保存するという意味？

いいえ。保存失敗を例外として知らせる。

### Q4. フォームの投稿処理ではどちらが使いやすい？

`save`。`if` で保存成功・失敗を分岐できるため。

### Q5. 保存に失敗した理由を確認するには？

```ruby
board.errors.full_messages
```

バリデーションエラーの内容を取得できる。