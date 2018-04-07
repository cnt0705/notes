# macOS で Ruby (+ rbenv, Bundler, Sinatra) の開発環境構築

※ Homebrew がインストールされている前提

## rbenv のインストール

```
brew doctor
brew install rbenv
brew install ruby-build
```

### rbenv

マシンにインストールされた異なるバージョンの Ruby を、ディレクトリごとに切り替えることができる。

### ruby-build

rbenv で Ruby をインストールするためプラグイン。

## .zshrc もしくは .bash_profile に追記

```
export PATH="$HOME/.rbenv/bin:$PATH"
eval "$(rbenv init -)"
```

rbenv へのパスを通し、初期化スクリプトを記述する。  
See also: [How rbenv hooks into your shell](https://github.com/rbenv/rbenv#how-rbenv-hooks-into-your-shell)

## Ruby のインストール

e.g. version 2.5.0 をインストールしたい場合

```
rbenv install 2.5.0
rbenv global 2.5.0
```

`rbenv install` で指定したバージョンの Ruby をインストールする。  
Ruby のバージョン指定は、

- 環境全体で使用する場合は `rbenv global`
- ディレクトリ内（プロジェクト固有）のみで使用する場合は `rbenv local`

で行える。

## 不要な Gem をアンインストール

```
gem uninstall -I -a -x --user-install --force
```

Gem を Bundler で管理したかったので（後述）、グローバルにあるものをいったん掃除する。

## Bundler をインストール

```
gem install bundler
```

### Gem

Ruby 用のパッケージ管理ツールで、ライブラリの作成や公開、インストールを支援する。

### Bundler

Gem 同士の互換性を保ちながら、Gem パッケージの種類やバージョンを管理する。  
複数の PC で同様の Gem をインストールしたり、バージョンを統一することができる。

## Gemfile を作成

```
bundle init
```

Bundler 用の設定ファイル Gemfile が作成される。

## インストールしたい Gem を Gemfile に追記

```
gem "sinatra"
gem "sinatra-contrib"
```

## Bundler を通して Gem をインストール

```
bundle install --path vendor/bundle
```

Bundler は Gemfile の記述にしたがって Gemfile.lock を生成する。  
Gemfile.lock には、インストールしたライブラリと、依存関係にあるすべてのライブラリの一覧とバージョンが記載されている。  
`--path` オプションで任意のディレクトリに Gem をインストールできる。

## Hello world

```ruby
require 'sinatra'
get '/' do
  "Hello world"
end
```

```
bundle exec ruby hello.rb
```

Bundler を通してインストールした Gem の場合は、実行コマンドの先頭に `bundle exec` をつける必要がある。

## 参考

- [GitHub - rbenv](https://github.com/rbenv/rbenv)
- [rbenvは何をしているのか？](http://d.hatena.ne.jp/zariganitosh/20141101/what_does_rbenv)
- [rbenv + ruby-build はどうやって動いているのか](http://takatoshiono.hatenablog.com/entry/2015/01/09/012040)
- [頭が弱すぎてruby + rbenv + gem + bundle + (+rails)の仕組みが理解できない・・・](http://komaken.me/blog/2013/07/05/%E9%A0%AD%E3%81%8C%E5%BC%B1%E3%81%99%E3%81%8E%E3%81%A6ruby-rbenv-gem-bundle-rails%E3%81%AE%E4%BB%95%E7%B5%84%E3%81%BF%E3%81%8C%E7%90%86%E8%A7%A3%E3%81%A7%E3%81%8D%E3%81%AA%E3%81%84%E3%83%BB/)
