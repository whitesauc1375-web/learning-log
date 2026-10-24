

---
created: "2026-10-24"
tags:
  - 未理解
---

# pg gem

## 📌 一言でいうと

Rubyからデータベースの**PostgreSQLへ接続し、データをやり取りするためのgem**

gemは、Rubyで利用できる追加機能をまとめたライブラリのこと

`pg`は、RubyとPostgreSQLの間で通信を支える役割を持つ

**分類：データベース接続・ライブラリ**

モデル・ビュー・コントローラーそのものには分類されない  
Railsでは、モデルがデータベースを操作するときに裏側で使われる

## 📖 仕組み・役割

Railsでは、`User.create`などのメソッドを使ってデータを保存できる

PostgreSQLを利用している場合、その処理を支えるのがActive Recordと`pg` gem

それぞれの役割は次のとおり

|要素|役割|
|---|---|
|モデル|ユーザーや投稿などのデータと、そのルールを扱う|
|Active Record|モデルの操作をSQLに変換するなど、データベース操作を担当する|
|`pg` gem|PostgreSQLとの接続やSQLの送信、結果の受け取りを担当する|
|PostgreSQL|SQLを実行し、データの保存や検索を行う|

厳密には、Active Recordの**PostgreSQL用アダプター**が`pg` gemを利用する  
アダプターとは、データベースごとの違いに対応するための仕組み

通常のRails開発では、`pg`を直接操作するコードを書く機会は少なく、モデル経由で利用する

## 💻 コード例

### ① Gemfileに追加する

```
# Gemfile
gem "pg"
```

`gem "pg"`は、このアプリで`pg` gemを利用するという宣言

追加後、ターミナルで次のコマンドを実行する

```
bundle install
```

BundlerがGemfileの指定に従ってgemをインストールする [Heroku Dev Center](https://devcenter.heroku.com/articles/connecting-heroku-postgres?utm_source=chatgpt

### ② Railsの接続先を設定する

```
# config/database.yml
# ローカル開発用の設定例
development:
  adapter: postgresql
  encoding: unicode
  database: sample_app_development
  host: localhost
  username: <%= ENV["DB_USERNAME"] %>
  password: <%= ENV["DB_PASSWORD"] %>
```

|設定|意味|
|---|---|
|`adapter: postgresql`|PostgreSQL用のアダプターを使う|
|`database`|接続するデータベース名|
|`host`|PostgreSQLが動いている接続先|
|`username`|接続に使うPostgreSQLのユーザー名|
|`password`|接続に使うパスワード|

`ENV["DB_USERNAME"]`などは、環境変数から設定値を読み取る記述  
接続先や認証情報は、自分の開発環境に合わせて設定する

### ③ モデルを通してデータを保存する

```
# Userモデルとusersテーブルにname属性がある前提
User.create(name: "裕也")
```

PostgreSQLを使うRailsアプリでは、この操作の裏側で`pg` gemが通信を支える

モデルごとに`gem "pg"`を書く必要はない


## 🎯 使用場面

- RailsアプリのデータベースにPostgreSQLを使うとき
- RubyプログラムからPostgreSQLへ接続するとき
- PostgreSQLへデータの保存・検索・更新・削除を行うとき

Rails専用のgemではなく、Railsを使わないRubyプログラムでも利用できる


## ⚠️ 注意点・よくある間違い

**`pg` gemとPostgreSQL本体は別のもの**

`pg`をインストールしても、PostgreSQLのサーバーが用意されるわけではない  
ローカル環境・Docker・外部サービスなどで、接続先のPostgreSQLを別途用意する必要がある

**Gemfileとdatabase.ymlでは書く名前が異なる**

```
# Gemfile
gem "pg"
```

```
# config/database.yml
adapter: postgresql
```

gem名は`pg`、Railsのアダプター名は`postgresql`

**すべてのデータベースで使えるわけではない**

`pg`はPostgreSQL用  
MySQLやSQLiteを使う場合は、それぞれに対応したgemを利用する

**Dockerでは`localhost`の意味に注意する**

RailsとPostgreSQLが別々のコンテナで動いている場合、`localhost`はRails側のコンテナ自身を指す  
接続先には、構成に応じてデータベース側のサービス名などを指定する

## 🔗 関連する知識

- [[gem]]
- [[Gemfile]]
- [[Bundler]]
- [[bundle install]]
- [[PostgreSQL]]
- [[Active Record]]
- [[Model]]
- [[database.yml]]
- [[SQL]]
- [[環境変数]]
- [[Docker]]



## 💡 自分がつまずいたところ

学習時に確認しておきたいポイント

- `pg`はデータを保存する本体ではなく、PostgreSQLとの通信を担当するgem
- `gem "pg"`を書く場所はモデルではなくGemfile
- Active Recordと`pg`は別の役割を持ち、組み合わせて使われる
- gemを導入するだけでなく、PostgreSQLの用意と接続設定も必要


## ✅ 理解度チェック
- [ ] 自分の言葉で説明できる
- [ ] コードを読んで理解できる
- [ ] 自分でコードを書ける
- [ ] `pg` gemとPostgreSQL本体の違いを説明できる
- [ ] Gemfileとdatabase.ymlの役割を区別できる


## 🔄 復習メモ

### Q1. `pg` gemは何をするもの？

RubyからPostgreSQLへ接続し、SQLを送信したり結果を受け取ったりするライブラリ

### Q2. `gem "pg"`はどこに書く？

アプリが利用するgemを管理するGemfileに書く

### Q3. `pg`をインストールすればPostgreSQLも使える状態になる？

それだけでは使えない  
接続先のPostgreSQLを別途用意し、接続設定を行う必要がある

### Q4. モデル・ビュー・コントローラーのどれに分類される？

どれかそのものではなく、データベース接続を担当するライブラリに分類される  
Railsではモデルのデータベース操作を裏側で支える

### Q5. Active Recordと`pg` gemの違いは？

Active Recordはモデルを通したデータベース操作を担当する  
`pg` gemは、その操作に必要なPostgreSQLとの通信を担当する