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
モデルオブジェクトが未保存のデータであれば、create アクションに向けた宛先の情報を生成し、
保存済みのデータであればその更新のためのアクションに向けた宛先の情報を生成する。

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
`persisted?` でオブジェクトが保存済みかどうか、
`changed?` でオブジェクトが変更済みかどうかを確認できる。

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
