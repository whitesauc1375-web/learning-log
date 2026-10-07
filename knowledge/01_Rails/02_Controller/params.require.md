


---
created: "2026-10-07"
tags:
  - 未理解
---

# params.require

## 📌 一言でいうと

**リクエストで送られてきたデータの中に、指定した項目が存在するか確認するメソッド**

`require` は、Railsの**ストロングパラメータ（Strong Parameters）という仕組みで使用されるメソッドの1つ**
## 📖 仕組み・役割

```ruby
params.require(:user)
```
- `params` → フォームなどから送信されたデータ
- `require(:user)` → `user`という項目があるか確認する
- `permit` → 保存・更新に使用してよい項目を指定する

例えば、フォームから以下のデータが送られてきた場合
```ruby
{
  user: {
    email: "test@example.com",
    last_name: "田中",
    first_name: "太郎",
    avatar: "画像データ"
  }
}
```

`require(:user)`で`user`のデータを取り出し、`permit`で使用できる項目を制限する


## 💻 コード例

```ruby
def profile_params
  params.require(:user).permit(
    :email,
    :last_name,
    :first_name,
    :avatar
  )
end
```
**コードの意味**

|コード|役割|
|---|---|
|`def profile_params`|メソッドを定義する|
|`params`|送信されたデータを取得する|
|`require(:user)`|`user`のデータが存在するか確認する|
|`permit(...)`|使用を許可する項目を指定する|
|`end`|メソッドの定義を終了する|

## 🎯 使用場面

ユーザー登録やプロフィール更新などで、フォームから受け取ったデータを安全に保存・更新するとき

## ⚠️ 注意点・よくある間違い

**① Rubyのrequireとは別物**

```ruby
require 'date'
```
→ Rubyのライブラリを読み込む

```ruby
params.require(:user)
```
→ Railsで`user`というパラメータが存在するか確認する

**② requireとpermitは役割が違う**

- `require` → 必要な項目を確認する
- `permit` → 使用できる項目を制限する

**③ userが存在しない場合**

`require(:user)`は、`user`が存在しない、または値が空の場合などに`ActionController::ParameterMissing`というエラーを発生させる

## 🔗 関連する知識
- [[params]]
- [[permit]]
- [[Strong Parameters]]
- [[Controller]]



## 💡 自分がつまずいたところ

`require`はRubyでファイルを読み込む命令だと思っていた

しかし、今回の`params.require(:user)`は別のメソッドで、送信されたパラメータから必要な項目を取り出すために使う


## ✅ 理解度チェック
- [ ] 自分の言葉で説明できる
- [ ] コードを読んで理解できる
- [ ] 自分でコードを書ける


## 🔄 復習メモ

**Q1.** `**params.require(:user)**`**は何をする？**

**Q2.** `**require**`**と**`**permit**`**の違いは？**

**Q3. なぜ**`**permit**`**で使用できる項目を制限する必要がある？**