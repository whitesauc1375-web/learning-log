# ActionMailer

## 📌 一言でいうと
Railsでメール送信機能を作るためのフレームワーク

## 📖 何ができる？
- ユーザー登録メールの送信
- パスワード再設定メールの送信
- お問い合わせ通知メールの送信
- メール本文や件名の作成

## 💻 よく使うコード

```ruby
class UserMailer < ApplicationMailer
  def welcome_email
    mail(to: @user.email, subject: "登録ありがとうございます")
  end
end
```