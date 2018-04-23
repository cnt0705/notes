# Ruby on Rails チュートリアル

## 第1章

### Rails 5.2.0 インストール

`bundle init`で Gemfile を作成する。
`gem 'rails'` を追記して `bundle install --path vendor/bundle` で Gem をインストールする。

### スケルトンプロジェクト作成

```
bundle exec rails new hello_app
```

`rails new` を通して作成された Gemfile のなかに `gem 'rails'` があって一瞬混乱した 🙄
`bundle install` がはしってグローバルに Gem がいろいろぶちこまれているし…

### Gem インストールやり直し

[[Sy] Railsの環境構築でグローバルのgemを汚さずにプロジェクト内にbundle install する手順](https://utano.jp/entry/2016/07/rails-bundle-install-gem-inner-project/)を参考にしてインストールやり直し。

### Rails のルーティング

https://railsguides.jp/routing.html
> Rails のルーターは受け取った URL を認識し、適切なコントローラ内アクションに割り当てます。

## 第2章

### CSRF 対策

```
protect_from_forgery with: :exception
```

Rails が生成するすべてのフォームや Ajax リクエストで
自動的にセキュリティトークンを含める。
もしセキュリティトークンが想定されている値と一致しなければ、
例外（エラー）が投げられる。

### CSRF とは

Cross-site Request Forgery

- [安全なウェブサイトの作り方](https://www.ipa.go.jp/files/000017316.pdf)
- [IPA リクエスト強要（CSRF）対策](https://www.ipa.go.jp/security/awareness/vendor/programmingv2/contents/301.html)
- [［はまちちゃんのセキュリティ講座］ここがキミの脆弱なところ…！](http://gihyo.jp/dev/serial/01/hamachiya2/0001)

### REST

アプリケーションの設計方法の一つ。
操作の**対象**となるリソースを URL で、操作の**方法**を HTTP メソッドで表現する。

### ルーティング

URL と HTTP メソッドの組み合わせをアクションと紐づける仕組み。

```
GET    'sample' => 'books#index'
POST   'sample' => 'books#create'
DELETE 'sample' => 'books#destroy'
PUT    'sample' => 'books#update'
```

### リソース

データモデルと Web インターフェースが組み合わさったもの。
リソースは「対象の情報を HTTP プロトコル経由で CRUD 操作できるオブジェクト」とみなされる。
Rails では `resources` メソッドを使うことで、リソースをベースにしたルーティングを爆速設定できる。

### Scaffold

Rails のアプリケーションではモデル・ビュー・コントローラのそれぞれ作成し、必要なルーティングの設定をする必要がある。 
Scaffold は上記ファイルの雛形作成や設定を自動で行う機能である。

### MVC の挙動

1. ブラウザがコントローラにリクエストを送る
1. ルーターがリクエストを対象のアクションに割り当てる
1. コントローラが対象のアクションを実行する
1. コントローラがモデルに DB 問い合わせを要求する
1. モデルが DB 操作を行う
1. モデルがコントローラに結果を返却する
1. コントローラがビューにデータを渡す（インスタンス変数にデータを格納する）
1. ビューが HTML をレンダリングする
1. ビューがコントローラに HTML を返却する
1. コントローラがブラウザにレスポンスを返す

### Active Record

Rails で採用されている OR Mapper のこと。
SQL を書くことなく DB にアクセスできる Ruby のライブラリ。
ライブラリ名は Active Record デザインパターンに由来する。

### Association

Active Record モデル同士のつながりを指す。
Active Record の関連付け機能を使用すると、モデル同士につながりがあることを Rails に対し明示的に宣言する。
よって、モデルの操作を一貫させることができる。

### ActionController::Base

各コントローラが継承する基本クラス。
モデルオブジェクトの操作、HTTP リクエストのフィルタリング、ビューを HTML として出力する機能などを実行できる。

### ActiveRecord::Base

各モデルが継承する基本クラス。
モデルオブジェクトが DB にアクセスできたり、DB のカラムを Ruby の属性のように取り扱うことができる。

## 第3章

### ルーティング

```ruby
Rails.application.routes.draw do
  get  'static_pages/home'
  root 'application#hello'
end
```

`/static_pages/home` という URL に対する GET リクエストを
StaticPages コントローラの home アクションと結びつける。

```ruby
class StaticPagesController < ApplicationController
  def home
  end
end
```

home アクションは空が、（Rails 特有のふるまいとして）ビューを返す。

### ジェネレータ

機能を開発する上で雛形になるソースコードを自動生成する。
`rails generate` の引数によって (scaffold, controller, etc) 生成するファイル群が変わる。

[いつも忘れる「Railsのgenerateコマンド」の備忘録](http://maeharin.hatenablog.com/entry/20130212/rails_generate)

### 自動化テスト

`rails generate` を実行するとテストファイルが同時に作成される。
何かを作成・変更するときはテストを同時に書く修正をつけよう。

[結局テストはいつ行えばよいのか](https://railstutorial.jp/chapters/static_pages?version=5.1#aside-when_to_test)

### Helper

Rails にデフォルトで組み込まれているメソッドのこと。

```ruby
Rails.application.routes.draw do
  get  'static_pages/home'
end
```

ルートを追加すると自動的に下記のヘルパーが使えるようになる。

```
static_pages_home_url
```

### Set up

`setup` はテストが実行される直前で呼び出されるメソッドのこと。

### 埋め込み Ruby

ERB (Embedded Ruby) は Web ページに動的な要素を加えるときに使うテンプレートシステムである。
`<% ... %>` は中に書かれたコードを実行するのみで、何も出力しない。
`<%= ... %>` は中のコードの実行結果がテンプレートのその部分に挿入される。

```erb
<% provide(:title, "Home") %>
<title><%= yield(:title) %> | Ruby on Rails Tutorial Sample App</title>
```

`provide` ヘルパーは layout にパラメータを渡し、`yield` ヘルパーでそれを受け取る。

```erb
<body>
  <%= yield %>
</body>
```

レイアウトファイル内の `<%= yield %>` には各ページの内容が挿入される。

### csrf_meta_tags

CSRF 対策のため。
トークンを含むメタタグを自動で生成する。

## 第4章

### リテラル

ダブルクォート → 中の変数や式が展開される
シングルクォート → 中の変数や式が展開されない

```ruby
puts "こんにちは\nこんにちは"
#=> こんにちは
#   さようなら

puts 'こんにちは\nこんにちは'
#=> こんにちは\nさようなら
```

### オブジェクト

Ruby ではあらゆるものがオブジェクトである。
オブジェクトとは「（いついかなる場合にも）メッセージに応答するもの」とである。

### 論理値

オブジェクトそのものが false となるのは false 自身と nil のみ。
0 ですら true となる。

```ruby
>> !!0
=> true
```

### メソッド

引数をとるメソッドであっても、デフォルト値が設定されている場合はかっこなしの呼び出しが可能である。

```ruby
foo.bar
```

Ruby のメソッドでは、最後に評価された値が返る。
明示的に return を書かなくてよい（書かない方が主流）

### モジュール

関連するメソッドをまとめる方法の一つ。
is-a 関係が成り立たないクラス間で共通の処理がある場合、
継承を使うのではなくモジュール（ミックスイン）を include するとよい。
Rails では自動でヘルパーモジュールを読み込むので、include する必要はない。

 ### 配列

Array に対する sort メソッドや reverse メソッドは元の配列を変更しない。
破壊的なメソッドを使いたい場合は、元のメソッドの末尾に `!` をつける。

### ブロック

ワンライナーの場合は波カッコで、復習行にわたる場合は do..end 記法を使うのが Ruby の慣習である。

### シンボル

シンボルは一見すると文字列のようなデータ形式である。
内部的には整数なので、文字列よりも高速に値を比較できる。
同じシンボルは同じオブジェクトを指すため、メモリの使用効率がいい。
シンボルはイミュータブルである。
ただし文字列と違って、すべての文字が使えるわけではない。

### ハッシュ

ハッシュではシンボルをキーとして使うことが一般的である。
version 1.9 以降では JS ライクなコロンを使う書き方も導入された。

```ruby
>> h1 = { :name => "Michael Hartl", :email => "michael@example.com" }
=> {:name=>"Michael Hartl", :email=>"michael@example.com"}
>> h2 = { name: "Michael Hartl", email: "michael@example.com" }
=> {:name=>"Michael Hartl", :email=>"michael@example.com"}
>> h1 == h2
=> true
```

### CSS 呼び出しの行を読み解く

```erb
<%= stylesheet_link_tag 'application', media: 'all', 'data-turbolinks-track': 'reload' %>
```

`stylesheet_link_tag` メソッドの引数のカッコは省略されている。
ハッシュがメソッド呼び出しの最後の引数の場合、波カッコを省略できる。

```ruby
# 下記のコードと等価である
stylesheet_link_tag('application', { media: 'all', 'data-turbolinks-track': 'reload' })
```
