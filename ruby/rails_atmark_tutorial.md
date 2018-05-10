# アプリ作成時のメモ

## Rake

Ruby 製のビルドプログラム。  
定められた一連の処理を「タスク」という単位でまとめて実行することができる。

## ルーティング定義の割り当てを確認する

```
rake routes
```

もしくは下記にアクセスする。

```
/rails/info/routes
```

## モデルを作成する

```
rails generate model
```

上記コマンドに `単数形のモデル名 カラム名:型 カラム名:型...` の形式で引数を渡すと、モデルを作成できる。  
モデル作成時に作られるファイルは下記の通りである。

- app/models/user.rb
  - モデルファイル
- db/migrate/20180507210649_create_users.rb
  - マイグレーションファイル
- test/fixtures/users.yml
  - 初期データ投入用ファイル
- test/models/user_test.rb
  - モデルのテストファイル

また、DB のスキーマ定義が更新される。

- db/schema.rb

## データを登録する

```ruby
user = User.new(name: "アリス", department: "ネットワーク管理部")
user.save
```

`new` メソッドにより、引数の値で初期化されたモデルオブジェクトを作成する。  
`save` メソッドで DB に保存する。  
`create` メソッドは上記の手順をまとめて実行する。

## コントローラーを作成する

```
rails generate controller
```

上記コマンドに `コントローラー名（特定のモデルに関するものであれば複数形）` の形式で引数を渡すと、コントローラーを作成できる。

- app/assets/javascripts/users.coffee
  - JS ファイル
- app/assets/stylesheets/users.scss
  - CSS ファイル
- app/controllers/users_controller.rb
  - コントローラーファイル
- app/helpers/users_helper.rb
  - ヘルパーファイル
- test/controllers/users_controller_test.rb
  - コントローラーのテストファイル

## ルーティングを設定する

`resources` メソッドは第 1 引数に渡したシンボルの RESTful な URL をまとめて生成する。  
デフォルトでは index, show, new, create, edit, update, destroy の 7 アクションと対応するルーティングを作る。  
上記のアクションがすべて作られていない場合は `only` オプションでアクションを個別に指定できる。

```ruby
Rails.application.routes.draw do
  resources :books
  # resources :users, only: [:index]
  resources :users, only: %i(index)
end
```

## フォーム要素を作成する

`form_for` で引数に取ったモデルオブジェクトのための `<form>` タグを生成する。  
モデルオブジェクトが未保存のデータであれば、create アクションに向けた宛先の情報を生成し、保存済みのデータであればその更新のためのアクションに向けた宛先の情報を生成する。

`form_tag` は任意のモデルに基づかない form を作るときに使う。  
cf. [【Rails】form_for/form_tagの違い・使い分けをまとめた](https://qiita.com/shunsuke227ono/items/7accec12eef6d89b0aa9)

## Strong Parameters

DB に入れるパラメータの値を制限する。

```ruby
params.require(:user).permit(:name, :email)
```

「`params` が `:user` というキーを持ち、`params[:user]` は `:name` および `:email` というキーを持つハッシュであること」を検証する。  
例えば `params[:user]` が `{:name => 'taro', :email => 'taro@example.com'}` のようなハッシュである場合に検証 OK となる。

## バリデーション

create, save, update メソッドなどの呼び出し時にバリデーションチェックを行う。  
値が不正であれば false を返す。  
バリデーションの内容はモデルに記載する。

```ruby
class Person < ActiveRecord::Base
  validates :name, presence: true
end
```

## データを一件取得する

`find` メソッドは引数の値を主キーとして、持っているレコードをモデルオブジェクトとして返す。

## アクションへのリンクを作成する

`link_to` は第 1 引数にアンカータグで表示する文字列、第 2 引数に href 属性値を渡すことでリンクを生成できる。

## リンクパスを生成する

`*_path` メソッドは `rake routes` の出力の第 1 項目を接頭辞としてつけることで、その URL を生成する。

```ruby
# /users
users_path
# /user/new
new_user_path
# /user/:id
user_path(:id)
# /user/:id/edit
edit_user_path(:id)
```

## データを更新する

```ruby
@user = User.find(params[:id])
@user.update(user_params)
```

`find` メソッドで更新対象のデータを取得する。  
`update` メソッドに渡した引数のハッシュのキーと一致するパラメータを、その値で更新する。

## データを削除する

```ruby
@user = User.find(params[:id])
@user.destroy
```

`find` メソッドで削除対象のデータを取得する。  
削除用のリンクは下記の通り。

```ruby
# /user/:id に対して DELETE メソッドでリクエストを送信する
link_to '削除', user_path(user), method: 'DELETE'
```

## フィルター

アクションのコールバックをフィルターと呼ぶ。  
メソッドの呼び出し前後で必ず実行する処理を宣言できる。  
呼び出すメソッドは private メソッドとしてコントローラー中に定義する。  
cf. [Rails4でアクションの前後にフィルタ/処理を挟み込む](http://ruby-rails.hatenadiary.com/entry/20141129/1417223453)

```ruby
before_action :method_name  # メソッドの前に行うコールバック
after_action :method_name   # メソッドの後に行うコールバック
around_action :method_name # メソッドの前後に行うコールバック
```

```ruby
# only オプションで指定したアクション実行前に set_user を実行する
# before_action :set_user, only: ["show", "edit", "update", "destroy"]
before_action :set_user, only: %w(show edit update destroy)

private
  def set_user
    @user = User.find(params[:id])
  end
```

## 部分テンプレート

ビューの共通部分を別ファイルとして切り出す。  
ファイル名の先頭には `_form.html.erb` にようにアンダースコアをつけ、読み込み側のビューで `render` メソッドを呼び出す。

```erb
<%= render 'form' %>
```

## ActiveRecord

データベース上のテーブルを 1 つのクラスにマッピングする O/R マッパー。  
モデルオブジェクトは属性としてテーブルのカラムをマッピングしている。  
それらの属性はオブジェクト生成時に初期化したり、パブリックなインスタンス変数として使うことができる。

### new メソッド

`AcitveRecord::Base` サブクラスのインスタンス＝モデルオブジェクトを作成する。  
属性をキーとするハッシュを引数にとり、その値で属性を初期化できる。

### save メソッド

`new` メソッドで作成されたモデルオブジェクトを DB に保存する。  
未保存のオブジェクトの場合は INSERT を、保存済みのオブジェクトの場合は UPDATE を実行する。  
`persisted?` でオブジェクトが保存済みかどうか、`changed?` でオブジェクトが変更済みかどうかを確認できる。

### update メソッド

属性をキーとするハッシュを引数にとり、オブジェクトの変更と DB の更新を同時に行う。

### destroy メソッド

オブジェクトを DB から削除する。  
`destroyed?` でオブジェクトが削除済みかどうかを確認できる。

### find メソッド

DB にあるテーブル上のレコードの 1 つをモデルのオブジェクトとして取り出す。  
主キーを引数として渡して、取り出されるレコードを指定する。

### where メソッド

カラムの値に条件を付け、該当するレコードをすべて取得する。  
第 1 引数に条件である文字列、第 2 引数では第 1 引数のプレースホルダーに当たる値を渡す。  
引数に OR を使うことや、メソッドチェインも可能である。

### pluck メソッド

属性値のみを取得して配列として返却する。  
実行するたびに SQL を発行するため、インスタンスからデータを取得する場合は map メソッドを使用するほうがローコストである。  
cf. [pluckよりもmapのほうが高速なケース](https://qiita.com/metheglin/items/18064851a8f00dab67f8)

### order メソッド

複数のレコードを取得する際、任意のカラムの値に基づいてオブジェクトの並び方を指定できる。

### limit メソッド

複数のレコードを取得する際、取得するレコード数の上限を指定する。

### validates メソッド

DB への登録前に値が適切かどうかを検証する。  
`create!` など、`!` 付きのメソッドはバリデーション失敗時に例外をとばす。  
バリデーションエラーの内容は、オブジェクトに対して `errors.messages` を実行することで確認できる。  
すべてのエラーを一次元の配列で取得したい場合は `errors.full_messages` を使う。

```ruby
class User < ActiveRecord::Base
  # 値が空でないことを検証する
  validates :name, :department, presence: true
end
User.create(name: 'イブ', department: '人事部') # 保存される
User.create(department: '人事部') # 保存されない
```

### 便利なバリデーション

#### 必須入力

```ruby
validates :name, presence: true
```

#### 文字数が 2 文字以上、32 文字以下

```ruby
validates :name, length: { minimum: 2, maximum: 32, message: '2文字以上・32文字以下としてください' } 
```

#### 郵便番号の形式チェック

```ruby
validates :zip_code, format: { with: /^\d{3}\-?\d{4}$/ }
```

#### 数値かどうか

```ruby
# 属性が空のときはバリデーションをスキップ
validates :average, numericality: true, allow_nil: true
```

#### 値が指定の範囲に含まれるかどうか

```ruby
# Create 時のみチェックする
validates :age, inclusion: { in: 0..19, on: :create }
```

#### バリデーションの実行条件を定義

```ruby
validates :tel, if: :contact_by_phone?

# Boolean を返すメソッドの末尾には ? をつける
def contact_by_phone?
  contact_type == 'phone'
end
```

## ActiveRecord の関連

関連とはモデルオブジェクト同士のつながりを指す。

### 一対多

ある一つのモデルオブジェクトが多数の別のモデルオブジェクトから参照される。  
`belongs_to` で属するモデルオブジェクトを指定する。

```ruby
# user_id というカラムを作っておく

class Book < ActiveRecord::Base
  belongs_to :user
end

book = Book.create(title: "現場で使えるRuby on Rails")
book.user = User.create(name: "アリス", department: "開発部")
book.user # => #<User id: 1, name: "アリス", department: "開発部", ...>
book.user_id # => 1
```

User モデルから自身の主キーをもつ Book モデルを参照するには、`has_many` で関連を定義する。

```ruby
class User < ActiveRecord::Base
  has_many :books
end

user = User.create(name: "ボブ", department: "会計部")

# 結果は同じ
user.books.create(title: "現場で使えるRuby on Rails第二版")
user.books << Book.create(title: "現場で使えるRuby on Rails第三版")

user.books
# => #<ActiveRecord::Associations::CollectionProxy [
      # <Book id: 2, title: "現場で使えるRuby on Rails第二版", user_id: 3, ...>,
      # <Book id: 3, title: "現場で使えるRuby on Rails第三版", user_id: 3, ...>
    # ]>
 
user.books.build(title: "現場で使えるRuby on Rails第四版")
# => #<Book id: nil, title: "現場で使えるRuby on Rails第四版", user_id: 3, ...>
# 未保存の Book モデルオブジェクト
# 保存するには user.books.last.save などを呼び出す
```

### 一対一

一対多とほとんど同じ構成をとる。  
参照元モデルは `belongs_to` を、参照先モデルでは `has_one` を使って関連を定義する。

### 多対多

例えばデータにタグを付ける機能など。  
データは複数のタグを参照し、タグもまた複数のデータを参照できるような関連のことをいう。  
この場合、データとタグのような 2 つの対象のモデルとは別に、それらの接続情報を持つモデルが必要となる。

`has_many` の `:through` オプションで多対多の関連付けを行う。

```ruby
class Tag < ActiveRecord::Base
  has_many :books, through: :taggings
  has_many :taggings
end

class Tagging < ActiveRecord::Base
  belongs_to :tag
  belongs_to :book
end

class Book < ActiveRecord::Base
  has_many :tags, through: :taggings
  has_many :taggings
end
```

## 関連先を含めた検索

`joins` メソッドを使い、関連先のモデルを join する。  
`joins` メソッドの引数には関連名を、続く `where` メソッドの引数のハッシュのキーにはテーブル名を渡す。

```ruby
Book.joins(:tags).where(:tags: {name: 'Rails入門'})
# => #<ActiveRecord::Relation [
      # <Book id: 2, title: "Rails入門", ...>
    # ]>
```

### スコープ

よく使う検索条件は、モデル中にあらかじめ名前を付けて定義しておくことができる。

```ruby
class Book < ActiveRecord::Base
  has_many :taggings, foreign_key: 'tag_id'
  has_many :tags, through: :taggings
  scope :tagged_recommended, -> { joins(:tags).where(tags: {name: 'recommended'}) }
end
```

## ActionController のパラメータ

### クエリパラメータ

URLの末尾に `?` をつけ、`&` で区切ってパラメータを加える。

```
http://localhost:3000/admin?created_at_since=20140101&tagged=technology
```

上記のアクセスの場合、コントローラでパラメータを参照する `params` は下記のオブジェクトを返す。

```
params
=> {
  "created_at_since" => "20140101",
  “tagged” => “technology”,
  "action" => "index",
  "controller" => "admin/books"
}
```

### ルーティングパラメータ

ルーティングの定義で URL パターン内にパラメータとする部分を設定しておくことで加えられる。

```ruby
get ‘admin/:id’ => ‘admin/books#show’
```

```
http://localhost:3000/admin/2
```

上記のアクセスの場合、コントローラでパラメータを参照する `params` は下記のオブジェクトを返す。

```
params
=> {
  "controller" => "admin/books",
  "action" => "show",
  "id" => "2"
}
```

### POST データパラメータ

フォームのリクエストに含まれる。

```erb
<%= form_for @book, url: admin_path, method: 'post' do |f| %>
  <%= f.text_field :title %>
  <%= f.text_field :author %>
  <%= f.submit %>
<% end %
```

上記のフォームから送られるパラメータは下記のようになる。

```
params
=> {
  "utf8" => "✔",
  "authenticity_token" => "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
  "book" => {
    "title" => "新しい本",
    "author" => "新しい作者"
  },
  "commit" => "Create Book",
  "action" => "create",
  "controller" => "admin/books"
}
```

## ルーティング

Rails は  CRUD 機能を実装する RESTful な設計を標準としている。  
ルーティングでは `resources` を宣言するだけで、引数にとったリソースの CRUD 機能に必要なアクションとパスの対応が作られる。  
cf. [Railsのルーティングとフォーマット](https://qiita.com/Kokudori/items/d2bcb6fd24c662e73a33)

```
resources :books
 
# rake routes コマンドの実行結果
    books GET    /books(.:format)          books#index
          POST   /books(.:format)          books#create
 new_book GET    /books/new(.:format)      books#new
edit_book GET    /books/:id/edit(.:format) books#edit
     book GET    /books/:id(.:format)      books#show
          PATCH  /books/:id(.:format)      books#update
          PUT    /books/:id(.:format)      books#update
          DELETE /books/:id(.:format)      books#destroy
```

### 標準のアクション以外へのルーティング

標準のアクション以外や、従属するリソースへのルーティングも定義可能である。  
cf. [collectionとmemberのルートのちがい rails](http://it-memo.hatenablog.com/entry/2015/04/22/021301)

```ruby
resources :users, except: %i(index new create edit update destroy) do
  get ‘booking’, on: :collection
  post 'message', on: :member

  # 下記も同様の書き方
  # collection do
  #   get ‘booking’
  # end
  # member do
  #   post 'message', on: :member
  # end

  resources :books, only: %i(index)
end
 
# rake routes コマンドの実行結果
booking_users GET  /users/booking(.:format)        users#booking
 message_user POST /users/:id/message(.:format)    users#message
   user_books GET  /users/:user_id/books(.:format) books#index
         user GET  /users/:id(.:format)            users#show
```

### 名前空間付きルーティング

ルーティングの定義に名前空間を付けることができる。

```ruby
namespace :admin do
  resources :books
end
 
# rake routes コマンドの実行結果
admin_books GET  /admin/books(.:format)          admin/books#index
```

上記の場合は `app/controllers/admin/books_controller` の `Admin::BooksController` が呼ばれる。

## ビューの頻出ヘルパーメソッド

### image_tag

img タグを出力する。

```ruby
= imate_tag('logo.png', size: "222x111", alt: "BOOK LIBRARY")
# => <img src="/assets/logo.png", width="222" heigth="111" alt="BOOK LIBRARY" />
```

### url_for

URL を引数またはオプションにより生成・出力する。

```ruby
= url_for controller: 'books', action: 'index'
# => '/books'

= url_for books_path
# => '/books'

= url_for controller: 'books', action: 'show', id: 1
# => '/books/1'
```

### link_to

a タグを出力する。  
第 1 引数にリンクの文字列を渡し、第 2 引数で href 属性の値となる宛先を渡

```ruby
= link_to '書籍一覧', { controller: 'books', action: 'index' }
# => '<a href="/books">書籍一覧</a>'
```

### form_for

フォームを出力する。  
第 1 引数に対象のモデルオブジェクトを渡し、続けてオプションと 1 個のブロック変数を取るブロックを渡す。

```ruby
= form_for @book, url: admin_path do |f|
  = f.text_field :title
  = f.submit
# => <form accept-charset="UTF-8" action="/admin" class="edit_book" id="edit_book_2" method="post">
# =>   <div style="margin:0;padding:0;display:inline"><input name="utf8" type="hidden" value="✓">
# =>     <input name="_method" type="hidden" value="patch">
# =>     <input name="authenticity_token" type="hidden" value="xxxxxxxxx">
# =>   </div>
# =>   <input id="book_title" name="book[title]" type="text" value="hoge">
# =>   <input name="commit" type="submit" value="Update Book">
# => </form>
```

### text_field

テキストフィールドを出力する。

### submit

サブミットボタンを出力する。

## レイアウト

デフォルトのレイアウトは `app/views/layouts/application.htmls.erb` である。  
コントローラー側でレイアウトを変更できる。

```ruby
class Admin::BaseController < ApplicationController
  layout 'admin'
end
```

上記の場合は `app/views/layouts/admin.html.erb` がレイアウトとして選択される。

## ビュー実装時の注意点

### N+1 クエリ問題

例として、Book モデルと Author モデルがあり、Book モデルが Author モデルを参照している場合を考える。

```ruby
# コントローラー
@books = Book.all
 
# ビュー
- @books.each do |book|
  = book.title
  = book.author.name
```

ビューの最後の行で、Book モデルオブジェクトが未ロードの Author モデルオブジェクトを呼び出している。  
それによって、イテレーションのたびに DB への問い合わせが発生している。  
この処理だけでコントローラーでの 1 回と `@books` の要素数 N 回のクエリが発生し、パフォーマンスの低下を招く。

ActiveRecordの `include` メソッドであらかじめ関連モデルを読み込んでおく。  
cf. [似ているようで全然違う!?Activerecordにおけるincludesとjoinsの振る舞いまとめ](https://qiita.com/south37/items/b2c81932756d2cd84d7d)

```ruby
@books = Book.includes(:author)
```

## 国際化／多言語対応

Ruby I18n という Gem を用いる。  
`config/locales` に YAML 形式の言語別の設定情報（ロケール）を配置する。  
デフォルトロケールは `config/application.rb` で設定する。

```yml
en:
  hello: "Hello world"
  greeting:
    morning: "Good morning."
    daytime: "Hello."
    evening: "Good evening."
```

ロケールによる `hello` キーの翻訳をアプリケーション側で呼び出すのは下記の通り。

```ruby
I18n.t('hello')
# => "Hello world"

I18n.t('greeting.daytime')
# => "Hello."
```

ビューのパスを反映したロケールを書いて構造化すると、ビューではパスを省略したキーで済む。

```yml
ja:
  books:
    index:
      title: 書籍一覧
```

`app/views/books/index.html.slim` では次のように呼び出せる。

```ruby
= t('.title')
```
