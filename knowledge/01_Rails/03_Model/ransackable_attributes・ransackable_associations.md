

---
created: "2026-10-15"
tags:
  - 未理解
---

# ransackable_attributes・ransackable_associations

## 📌 一言でいうと

**Ransackで検索に使ってよい「自分の項目」と「関連先」を指定するメソッド**

- `ransackable_attributes` → そのモデル自身の検索可能な属性を指定する
- `ransackable_associations` → 検索でたどってよい関連を指定する

**分類：Model（モデル）**

どちらもモデルにクラスメソッドとして定義し、Ransackが検索条件を組み立てるときに参照する

## 📖 仕組み・役割

### `ransackable_attributes`の役割

そのモデルで、検索に使うことを許可する属性の名前を返す

基本的には、検索したいカラム名を文字列の配列で指定する

```ruby
def self.ransackable_attributes(auth_object = nil)
  ["name", "email"]
end
```

Userモデルにこの設定を書いた場合、`name`や`email`を使った検索を許可する

```ruby
User.ransack(name_cont: "太田").result
```

これは「名前に『太田』を含むユーザー」を検索する例

標準では、並べ替え可能な属性もこの設定をもとに決まる

### `ransackable_associations`の役割

検索条件でたどることを許可する、関連の名前を返す

```ruby
def self.ransackable_associations(auth_object = nil)
  ["boards"]
end
```

Userモデルにこの設定を書いた場合、Userに定義された`boards`という関連を検索に使える

ただし、**関連先のどの属性を検索できるかは、関連先のモデルでも許可する必要がある**

たとえば、掲示板のタイトルを条件にユーザーを検索する場合は、次の2つを設定する

|設定するモデル|必要な許可|
|---|---|
|User|`ransackable_associations`で`boards`を許可する|
|Board|`ransackable_attributes`で`title`を許可する|

このように、関連をたどる許可と、関連先の属性を検索する許可を組み合わせる [Ransack](https://activerecord-hackery.github.io/ransack/going-further/other-notes/?utm_source=chatgpt.com)

### メソッドの書き方を分解する

```ruby
def self.ransackable_attributes(auth_object = nil)
  ["name", "email"]
end
```

|部分|意味|
|---|---|
|`def`|メソッドを定義する|
|`self.`|この書き方では、モデルのクラスメソッドとして定義する|
|`ransackable_attributes`|Ransackが参照するメソッド名|
|`auth_object = nil`|引数を省略した場合は`nil`を使う|
|`["name", "email"]`|検索を許可する属性名の配列|
|`end`|メソッド定義を終える|

Rubyでは、最後に評価された式の値が戻り値になるため、ここでは配列が返される

`auth_object`は、利用者の権限などによって許可項目を切り替えたい場合に使える引数

**ログイン中のユーザーが自動的に入るわけではなく、使う場合は`ransack`のオプションで明示的に渡す**

今回の例では、引数を受け取る形だけ用意しており、処理には使っていない

## 💻 コード例

次の構成を前提にした例

- Userに`name`・`email`カラムがある
- Boardに`title`・`user_id`カラムがある
- UserとBoardに関連が定義されている

### Userモデルの設定

```ruby
# app/models/user.rb
class User < ApplicationRecord
  has_many :boards

  # User自身の検索可能な属性
  def self.ransackable_attributes(auth_object = nil)
    ["name", "email"]
  end

  # 検索でたどることを許可する関連
  def self.ransackable_associations(auth_object = nil)
    ["boards"]
  end
end
```

### Boardモデルの設定

```ruby
# app/models/board.rb
class Board < ApplicationRecord
  belongs_to :user

  # Board自身の検索可能な属性
  def self.ransackable_attributes(auth_object = nil)
    ["title"]
  end

  # Boardからさらに関連をたどる検索は許可しない
  def self.ransackable_associations(auth_object = nil)
    []
  end
end
```

### User自身の属性で検索する

```ruby
# 名前に「太田」を含むユーザーを検索する
@q = User.ransack(name_cont: "太田")
@users = @q.result
```

この検索では、Userの`ransackable_attributes`にある`"name"`を使う

### 関連する掲示板の属性で検索する

```ruby
# タイトルに「Rails」を含む掲示板を持つユーザーを検索する
@q = User.ransack(boards_title_cont: "Rails")
@users = @q.result(distinct: true)
```

`boards_title_cont`は、次のように分解できる

|部分|意味|
|---|---|
|`boards`|Userに定義された関連|
|`title`|Boardの検索対象の属性|
|`cont`|指定した文字を含むという条件|

**検索結果として取得する対象はBoardではなくUser**

掲示板のタイトルを条件にして、ユーザーを絞り込んでいる

`distinct: true`は、条件に合う掲示板を同じユーザーが複数持っている場合などに、ユーザーの重複を取り除くための指定



## 🎯 使用場面

- ユーザーの名前やメールアドレスを検索対象にするとき
- 掲示板のタイトルから、その掲示板を投稿したユーザーを探すとき
- 検索に使える属性や関連を必要な範囲に限定するとき
- 管理者と一般利用者で、検索可能な項目を切り替えるとき


## ⚠️ 注意点・よくある間違い

### 属性名と関連名を混同しない

```ruby
def self.ransackable_attributes(auth_object = nil)
  ["name", "email"]
end

def self.ransackable_associations(auth_object = nil)
  ["boards"]
end
```

- `name`・`email` → User自身の属性
- `boards` → `has_many :boards`で定義した関連

`ransackable_associations`に指定するものは、モデルのクラス名ではなく**関連名**

この例では`"Board"`ではなく`"boards"`を指定する

### 関連を許可するだけでは、関連先の全属性を検索できない

User側で`"boards"`を許可しても、Boardの全属性が検索可能になるわけではない

掲示板のタイトルを検索したいなら、Board側で`"title"`も許可する

### `[]`は「何も許可しない」という意味

```ruby
def self.ransackable_associations(auth_object = nil)
  []
end
```

この設定では、そのモデルから関連をたどるRansackの検索を許可しない

`has_many`や`belongs_to`による関連自体を削除する設定ではない

### この2つのメソッド自体が検索するわけではない

この2つは、検索可能な項目を返す設定用のメソッド

実際の検索条件は、`ransack`に渡して組み立てる

```ruby
@q = User.ransack(params[:q])
@users = @q.result
```

### 保存や閲覧の許可とは別

|設定・処理|担当すること|
|---|---|
|`ransackable_attributes`|検索に使える属性を指定する|
|`ransackable_associations`|検索でたどれる関連を指定する|
|Strong Parametersの`permit`|登録・更新時の一括代入に使えるパラメータを指定する|
|閲覧権限に応じた取得範囲の制限|誰がどのレコードを見られるかを制御する|

検索可能な項目を限定しても、それだけで他人のデータへのアクセスを防げるわけではない

### 検索に必要な項目だけを許可する

パスワード関連の値やトークンなど、検索に使わせる必要のない属性まで一括で許可しない

検索フォームの目的に合わせて、必要な属性や関連を選ぶ

公式資料でも、全カラムを検索可能にする設定では機密性の高いカラムまで対象になる点に注意を促している


## 🔗 関連する知識

- [[Ransack]]
- [[ransack・result]]
- [[Model]]


## 💡 自分がつまずいたところ

- `attributes`はモデル自身の属性、`associations`はモデルに定義した関連
- 関連名を許可することと、関連先の属性を許可することは別
- 掲示板のタイトルで検索しても、`User.ransack`なら取得対象はユーザー
- `auth_object = nil`には、ログイン中のユーザーが自動で入るわけではない
- どちらも検索そのものではなく、Ransackが参照する許可設定


## ✅ 理解度チェック
- [ ] 自分の言葉で説明できる
- [ ] コードを読んで理解できる
- [ ] 自分でコードを書ける
- [ ] 属性と関連の違いを説明できる
- [ ] 関連先の検索に必要な2つの許可を説明できる
- [ ] 検索項目の許可と、保存・閲覧の許可を区別できる

## 🔄 復習メモ

### Q1. `ransackable_attributes`は何をするメソッド？

そのモデル自身の属性について、Ransackの検索に使うことを許可する名前を返すメソッド

標準では並べ替え可能な属性の設定にも使われる

### Q2. `ransackable_associations`は何をするメソッド？

Ransackの検索でたどることを許可する関連名を返すメソッド

### Q3. この2つはどこに書く？

Modelにクラスメソッドとして定義する

### Q4. 掲示板のタイトルでユーザーを検索するには、何を許可する？

Userの`ransackable_associations`で`boards`を許可する

Boardの`ransackable_attributes`で`title`を許可する

### Q5. `boards_title_cont`はどういう意味？

関連する掲示板のタイトルに、指定した文字を含むという検索条件

### Q6. `ransackable_associations`が`[]`を返すとどうなる？

そのモデルから関連をたどるRansackの検索を許可しない

関連そのものが削除されるわけではない

### Q7. `auth_object = nil`はどういう意味？

許可項目を切り替えるための情報を受け取れるようにし、引数が省略された場合は`nil`を使うという意味