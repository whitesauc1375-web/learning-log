

---
created: "2026-10-07"
tags:
  - 未理解
---

# strong_parameters

## 📌 一言でいうと

**フォームから送られてきたデータのうち、保存・更新に使用してよい項目を制限するRailsの仕組み**

意図しないデータが勝手に変更されることを防ぐ

## 📖 仕組み・役割



## 💻 コード例

```ruby
params.require(:user).permit(:email, :last_name, :first_name, :avatar)
```

このコードには2つの重要なメソッドがある

| メソッド             | 役割                       |
| ---------------- | ------------------------ |
| `require(:user)` | `user`というデータがあるか確認して取り出す |
| `permit(...)`    | 使用を許可する項目を指定する           |

例えば、以下のデータが送信された場合
```ruby
{
  user: {
    email: "test@example.com",
    first_name: "太郎",
    admin: true
  }
}
```
`permit`で許可しているのは`email`や`first_name`などの4項目
そのため、`admin: true`は除外される

**つまり、許可していない項目を勝手に変更されないようにする仕組み**


## 🎯 使用場面

- ユーザー登録
- プロフィール更新
- 掲示板の投稿・編集
- コメントの作成・更新

**フォームから受け取ったデータをモデルに渡して保存・更新するとき**

## ⚠️ 注意点・よくある間違い

**① Strong Parametersはメソッド名ではない**

Strong ParametersはRailsの仕組みの名前
`require`や`permit`は、その仕組みで使用するメソッド


**② Strong Parameters自体はデータを保存しない**
```ruby
params.require(:user).permit(:email)
```

これは使用可能な項目を制限するだけ
実際の保存・更新には`save`や`update`などを使用する


**③ permitに書いていない項目は許可されない**
```ruby
permit(:email, :avatar)
```
この場合、`first_name`や`last_name`は許可されない


**④ Strong Parametersだけですべての安全性が保証されるわけではない**

許可する項目は必要最小限にする
また、入力値が正しいかを確認するバリデーションや、ユーザーの操作権限の確認も別途必要

## 🔗 関連する知識

- [[params.require]]    
- [[permit]]
- [[params]]
- [[Controller]]
- [[Model]]
- [[validation]]



## 💡 自分がつまずいたところ

`require`、`permit`、`Strong Parameters`の違いが分かりにくい

実際には、
- **Strong Parameters** → データを安全に扱うための仕組み
- **require** → 必要なデータを確認して取り出す
- **permit** → 使用してよい項目を指定する

と覚えると理解しやすい


## ✅ 理解度チェック
- [ ] 自分の言葉で説明できる
- [ ] コードを読んで理解できる
- [ ] 自分でコードを書ける


## 🔄 復習メモ

**Q1. Strong Parametersは何のために使う？**

**Q2.** `**require**`**と**`**permit**`**はそれぞれ何をする？**

**Q3.** `**permit**`**に指定していない**`**admin**`**が送信されたらどうなる？**

**Q4. Strong Parametersとバリデーションの違いは？**