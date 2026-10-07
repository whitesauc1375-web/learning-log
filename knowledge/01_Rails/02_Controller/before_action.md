

---
created: "2026-10-07"
tags:
  - 未理解
---

# before_action

## 📌 一言でいうと

**Controllerのアクションが動く前に、指定したメソッドを自動で実行する仕組み**


## 📖 仕組み・役割

例えば
```ruby
before_action :set_board
````
と書くと
`index` や `show` などのアクションが実行される前に

```ruby
set_board
```

というメソッドを先に実行できる
つまり

**毎回同じ処理を書くのを減らすための仕組み**

例えば
```ruby
def show
  @board = Board.find(params[:id])
end

def edit
  @board = Board.find(params[:id])
end

def update
  @board = Board.find(params[:id])
end
```
のように同じ処理を書く代わりに

```ruby
before_action :set_board, only: %i[show edit update]
```
とまとめられる



## 💻 コード例

```ruby
class BoardsController < ApplicationController
  before_action :set_board, only: %i[show edit update destroy]

  def show
  end

  def edit
  end

  def update
    @board.update(board_params)
  end

  def destroy
    @board.destroy
  end

  private

  def set_board
    @board = Board.find(params[:id])
  end
end
```
この場合
`show`、`edit`、`update`、`destroy`
が実行される前に
```ruby
set_board
```
が自動で実行される



## 🎯 使用場面

- 共通して同じデータを取得したいとき
- ログイン確認をしたいとき
- 権限チェックをしたいとき
- 複数のアクションで同じ処理を使いたいとき


## ⚠️ 注意点・よくある間違い

### ① `before_action` はControllerで使う

基本的にControllerに書く

```ruby
class BoardsController < ApplicationController
  before_action :set_board
end
```

### ② `only` を使うと対象を限定できる

```ruby
before_action :set_board, only: %i[show edit]
```

この場合
`show` と `edit` の前だけ実行される


### ③ `except` を使うと除外できる

```ruby
before_action :set_board, except: %i[index new]
```
この場合

`index` と `new` 以外で実行される


### ④ メソッド名の書き間違いに注意

```ruby
before_action :set_board
```

と書いたなら

```ruby
def set_board
end
```

というメソッドが必要



## 🔗 関連する知識

- [[Controller]]
- [[Action]]
- [[private]]
- [[params]]
- [[find]]
- [[only]]
- [[except]]



## 💡 自分がつまずいたところ

`before_action` が何かを保存するメソッドだと勘違いしやすい

実際には

**アクションの前に、指定したメソッドを自動で呼び出すための仕組み**

特に
```ruby
before_action :set_board
```
は

**「各アクションの前にset_boardを実行してね」**

という意味


## ✅ 理解度チェック
- [ ] 自分の言葉で説明できる
- [ ] コードを読んで理解できる
- [ ] 自分でコードを書ける


## 🔄 復習メモ

### Q1. `before_action` は何をする？

Controllerのアクションが実行される前に、指定したメソッドを実行する

### Q2. `before_action` はどこに書く？

Controller

### Q3. `only` は何のために使う？

実行するアクションを限定する

```
before_action :set_board, only: %i[show edit]
```

→ `show` と `edit` の前だけ実行される

### Q4. `except` は何のために使う？

実行しないアクションを指定する

### Q5. なぜ `before_action` を使う？

同じ処理を何度も書かずに済むため