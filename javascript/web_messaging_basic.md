# Web Messaging

- ブラウジングコンテキスト間（タブとタブの間など）でデータのやり取りを行うためのAPI
- HTTP通信ではありません
- JavaScriptではセキュリティの関係上、別タブの情報を取得することができない

## MessageChannel とは

- 通信相手と専用の回線＝チャンネルを作り、その中で通信を行う
- windowオブジェクトに対してメッセージを発行する必要がなくなり、相手の判別が不要になる
- MessagePortとは、チャンネルを通してメッセージを送るための窓口のようなもの

## メッセージを送る側のうごき

### 画面表示時

1. `var channel=new MessageChannel();` でMessageChannelのインスタンスを作る
1. 2つのMessagePortが作成され、それぞれ`channel.port1`, `channel.port2`プロパティに格納される
1. `window.opener`を通し、`postMessage`メソッドで`channel.port2`を送る ※1
1. `channel.port1.start();`で通信を開始する

### メッセージ送信時

1. `channel.port1.postMessage(value)`で相手のポート宛にメッセージを送る ※2

## メッセージを受け取る側のうごき

### 画面表示時

1. windowに`message`イベントをバインドする
1. `window.addEventListner`のコールバック関数
    1. 送信側画面から送られてきたMessagePortを受け取る ※1
    1. `port.start();`で通信を開始する
    1. `port`に`message`イベントをバインドする
1. `port.addEventListner`のコールバック関数
    1. ポート宛にきたメッセージを受け取る ※2

## 参考

http://uhyohyo.net/javascript/13_4.html
