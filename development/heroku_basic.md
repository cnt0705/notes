# Heroku 用語集

## Pipeline

一つのソースコードから環境ごとにアプリケーションをデプロイする仕組み。  
GitHub を使ったワークフローに最適化されている。

### Review app

Pull Request ごとのアプリケーション。

## Buildpacks

Heroku でアプリケーションをコンパイルするために使用するオープンソーススクリプト。

cf: [Heroku アプリケーションの全体像](https://qiita.com/opengl-8080/items/9dc9243c4e68cd0674f8#全体像)

## Dyno

Heroku アプリケーションが実際に実行されるプラットフォーム。  
実体は EC2 の巨大インスタンス上で動作する軽量 Linux コンテナ。  
Heroku の課金のための単位でもある。

cf: [Web, Worker などのプロセスタイプ](https://jp.heroku.com/dynos/configure)

## Add-ons

cf: [Herokuのアドオンと外部サービスを活用しよう](https://thinkit.co.jp/story/2011/04/01/2067)

## app.json

Heroku で構築するアプリケーションのメタ情報。  
新しいアプリケーションをデプロイするとき (Heroku Button, Review app stc.) に参照される。

## Heroku Buttons

GitHub 上にボタンを設置すると、クリックするだけど対象のリポジトリをデプロイできる。  
Heroku 側でボタンから送信されるリクエストの HTTP ヘッダ -> referer を参照し、  
app.json に基いてアプリケーションをデプロイする。
