


---
created: "2026-10-11"
tags:
  - 未理解
---

# nil？・empty？・blank？・present？

## 📌 一言でいうと

データが `nil` なのか、空なのか、値が存在するのかを確認する4つのメソッド。

## 📖 仕組み・役割

| メソッド       | 確認すること          | 分類     |
| ---------- | --------------- | ------ |
| `nil?`     | nilかどうか         | Ruby標準 |
| `empty?`   | 文字列や配列などが空か     | Ruby標準 |
| `blank?`   | nil・false・空白などか | Rails  |
| `present?` | blank?ではないか     | Rails  |

### 比較表

| 値         | nil?  | empty? | blank? | present? |
| --------- | ----- | ------ | ------ | -------- |
| `nil`     | true  | エラー    | true   | false    |
| `""`      | false | true   | true   | false    |
| `" "`     | false | false  | true   | false    |
| `"Rails"` | false | false  | false  | true     |
| `[]`      | false | true   | true   | false    |
| `[1, 2]`  | false | false  | false  | true     |
| `0`       | false | エラー    | false  | true     |
| `false`   | false | エラー    | true   | false    |


## 💻 コード例

```ruby
text = "  "

text.nil?
# => false

text.empty?
# => false

text.blank?
# => true

text.present?
# => false
```


**ポイント：** スペースだけの文字列は `empty?` では false、`blank?` では true になる。


### nilか確認したい

```ruby
user.name.nil?
```

### 文字列や配列が空か確認したい

```ruby
comments.empty?
```

### 未入力や空白を確認したい

```ruby
params[:keyword].blank?
```

### 値が存在する場合だけ処理したい

```ruby
if params[:keyword].present?
  # 検索処理
end
```


## 🎯 使用場面

### nilか確認したい

```ruby
user.name.nil?
```

### 文字列や配列が空か確認したい

```ruby
comments.empty?
```

### 未入力や空白を確認したい

```ruby
params[:keyword].blank?
```

### 値が存在する場合だけ処理したい

```ruby
if params[:keyword].present?
  # 検索処理
end
```

## ⚠️ 注意点・よくある間違い

- `nil.empty?` はエラーになる
- `0.present?` は true
- `false.blank?` は true
- `empty?` は空白だけの文字列を空と判定しない
- `present?` はRuby標準ではなくRailsのActive Supportが提供する


## 🔗 関連する知識

- [[疑問符（？）と感嘆符（！）]]
- [[Ruby]]
- [[ActiveSupport]]
- [[if]]
- [[unless]]
- [[nil]]
- [[validation]]
- [[presence]]




## 💡 自分がつまずいたところ

- `empty?` と `blank?` の違い
- `present?` と `blank?` の関係
- `nil` と空文字 `""` の違い


## ✅ 理解度チェック
- [ ] 自分の言葉で説明できる
- [ ] コードを読んで理解できる
- [ ] 自分でコードを書ける


## 🔄 復習メモ

### Q1. nil? は何を確認する？

値がnilかどうか。

### Q2. empty? は何を確認する？

文字列や配列などが空かどうか。

### Q3. blank? は何を確認する？

nil、false、空文字、空白だけの文字列などかどうか。

### Q4. present? は何を確認する？

blank?ではないかどうか。

### Q5. " ".empty? は？

false。スペースが3文字あるため。

### Q6. " ".blank? は？

true。空白だけの文字列だから。