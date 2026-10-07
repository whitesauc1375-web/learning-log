


---
created: "2026-10-07"
tags:
  - 未理解
---

# permit

## 📌 一言でいうと

**フォームから送られてきたデータのうち、使用を許可する項目を指定するメソッド**

RailsのStrong Parameters（ストロングパラメータ）で使用する

## 📖 仕組み・役割

```ruby
params.require(:user).permit(:email, :last_name, :first_name, :avatar)
```
- `params` → 送信されたデータ
- `require(:user)` → `user`のデータを確認して取り出す
- `permit(...)` → 使用を許可する項目を指定する

例えば、ユーザーがフォームから以下のデータを送信した場合
```ruby
{
  email: "test@example.com",
  last_name: "田中",
  first_name: "太郎",
  avatar: "画像データ",
  admin: true
}
```
`permit`で許可した4項目だけが残り、許可していない`admin`は除外される

## 💻 コード例

```ruby
def profile_params
  params.require(:user).permit(:email, :last_name, :first_name, :avatar)
end
```
**コードの意味**

| コード                  | 役割             |
| -------------------- | -------------- |
| `def profile_params` | メソッドを定義する      |
| `params`             | 送信されたデータ       |
| `require(:user)`     | userのデータを取り出す  |
| `permit(...)`        | 指定した4項目だけを許可する |
| `end`                | メソッドの定義を終了する   |


## 🎯 使用場面

- ユーザー登録
- プロフィール更新
- 掲示板の投稿・編集

フォームから受け取ったデータを、モデルの作成・更新処理に渡すとき

## ⚠️ 注意点・よくある間違い

**① permitはデータを保存するメソッドではない**
```ruby
permit(:email, :avatar)
```
使用できる項目を指定するだけで、データベースへの保存は行わない


**② permitに書いていない項目は除外される**
```ruby
permit(:email, :avatar)
```
この場合、`last_name`や`first_name`が送信されても許可されない


**③ requireとpermitは役割が違う**
- `require` → 必要なデータを確認して取り出す
- `permit` → 使用を許可する項目を指定する


## 🔗 関連する知識
- [[Strong Parameters]]
- [[params.require]]
- [[Controller]]
- [[params]]



## 💡 自分がつまずいたところ

`permit`はフォームから送信されたデータを保存するメソッドだと勘違いしやすい

実際には、**受け取ったデータの中から、使用を許可する項目を選ぶメソッド**


## ✅ 理解度チェック
- [ ] 自分の言葉で説明できる
- [ ] コードを読んで理解できる
- [ ] 自分でコードを書ける


## 🔄 復習メモ

**Q1.** `**permit**`**は何をするメソッド？**

**Q2.** `**permit**`**に指定していない項目はどうなる？**

**Q3.** `**require**`**と**`**permit**`**の違いは？**