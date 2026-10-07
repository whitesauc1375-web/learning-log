# letter_opener_web

## 📌 一言でいうと
Rails開発中に、送信したメールをブラウザ上で確認できるGem

## 📖 何ができる？
- 実際にメール送信せず内容を確認
- 送信先や件名、本文の確認
- 開発中のメール機能のテスト
- ブラウザ上でメール一覧を確認

## 💻 よく使うコード

```ruby
gem 'letter_opener_web'
```


ルーティング設定の例

```ruby
mount LetterOpenerWeb::Engine, at: "/letter_opener"
```