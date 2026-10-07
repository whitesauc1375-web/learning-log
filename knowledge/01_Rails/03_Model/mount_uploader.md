

---
created: "2026-10-07"
tags:
  - 未理解
---

# mount_uploader

## 📌 一言でいうと
`mount_uploader` は、**CarrierWaveというGemを使用して、Railsのモデルにファイルアップロード機能を紐づけるためのメソッド**

画像などのファイルを、モデルの特定のカラムを通じて扱えるようにする

## 📖 仕組み・役割
例えば、以下のコード

```ruby
mount_uploader :avatar, AvatarUploader
```

このコードは2つの引数を指定している

|コード|意味|
|---|---|
|`mount_uploader`|CarrierWaveが提供するメソッド|
|`:avatar`|アップロード機能を紐づける属性名|
|`AvatarUploader`|ファイルアップロードの処理・設定を担当するクラス|



## 💻 コード例

Userモデルでアバター画像を管理する場合
```ruby
class User < ApplicationRecord
  mount_uploader :avatar, AvatarUploader
end
```
これにより、Userモデルの`avatar`属性を通じてCarrierWaveのアップロード機能を利用できる


## 🎯 使用場面
- ユーザーのプロフィール画像
- 掲示板の投稿画像
- 商品画像
- 添付ファイル

などのアップロード機能を実装するとき


## ⚠️ 注意点・よくある間違い

### ① mount_uploaderだけでは不十分

```ruby
mount_uploader
```

これだけでは、どの属性にどのUploaderを紐づけるか指定できない

基本的には次のように記述する

```ruby
mount_uploader :avatar, AvatarUploader
```

### ② カラム名とUploaderクラス名は別物

- `:avatar` → モデルの属性
    
- `AvatarUploader` → アップロードを担当するクラス
    

この2つを混同しないこと

### ③ カラムが必要

通常、画像のファイル名などを保存するために、対応するデータベースカラムが必要になる

CarrierWaveの一般的なファイル保存方式では文字列型（string）のカラムを使用する
## 🔗 関連する知識

- [[CarrierWave]]
- [[Uploader]]
- [[ActiveRecord]]
- [[Model]]
- [[シンボル]]
- [[マイグレーション]]


## 💡 自分がつまずいたところ

**疑問：**
```ruby
mount_uploader :avatar, AvatarUploader
```

このコードの意味と、`mount_uploader` がどのような役割を持つのか理解できなかった

**理解したこと：**
`mount_uploader` はCarrierWaveが提供するメソッドで、モデルの属性にアップロード機能を紐づける

`:avatar` は属性名、`AvatarUploader` はアップロード処理を担当するクラスを指定している


## ✅ 理解度チェック
- [ ] 自分の言葉で説明できる
- [ ] コードを読んで理解できる
- [ ] 自分でコードを書ける

## 🔄 復習メモ
 **Q1.** `**mount_uploader**` **は何のGemが提供するメソッド？**

**Q2.** `**:avatar**` **は何を指定している？**

**Q3.** `**AvatarUploader**` **は何を担当する？**

**Q4.** `**mount_uploader :board_image, BoardImageUploader**` **と書いた場合、それぞれの引数は何を意味する？**