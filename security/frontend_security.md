# フロントエンドのセキュリティ

## XSS (Cross Site Scripting)

ユーザーのアクセス時に表示内容が生成される、動的な Web ページの脆弱性により発生する。  
攻撃者はユーザーを XSS 脆弱性のある標的 Web サイトに誘導し、悪意のあるスクリプトを閲覧者の環境で実行させる。  
具体的な攻撃手法は下記の 3 パターン存在する。

### Reflected XSS

ユーザーの入力値を動的に表示するページで起こりうる。  
エスケープ処理が疎かになっていると、ブラウザがスクリプトを解釈・実行してしまう恐れがある。

例えば、「検索結果条件のキーワードを GET パラメータで渡す」ページがあるとする。

```
http://example.com/?keyword=hogehoge
```

HTML 側ではキーワードを GET パラメータから取得し、表示する。

```html
<!-- hogehgoe の検索結果 -->
<h1>
 <?php echo $_GET['keyword'];>の検索結果
</h1>
```

この場合、ユーザーが下記の URL にアクセスすると、悪意あるスクリプトが実行できてしまう。

```
http://example.com/?keyword=<script>alert('XSS 発生')</script>
```

```html
<!-- 「XSS 発生」というアラートが表示される -->
<h1>
 <script>alert('XSS 発生')</script>の検索結果
</h1>
```

攻撃者は、

- 標的サイトとは別の Web サイトに不正な URL を張り、ユーザーからのアクセスを待つ
- 標的サイトを装って、不正な URL を記載したメールをユーザーに送り、アクセスを待つ

といった手段をとる。

### Stored XSS

ユーザーの入力値をデータベース等に保存するアプリケーションで起こりうる。  
保存した文字列を出力する際、エスケープ処理が疎かになっていると、ブラウザがスクリプトを解釈・実行してしまう恐れがある。

掲示板やユーザーフォーラムなど、不特定多数が書き込んだり、閲覧できるサイトが標的となる。  
悪意のあるスクリプトを含むページを閲覧したすべてのユーザーが被害者となりうる。  
Reflected XSS とは異なり、ユーザーに不正な URL をクリックさせる必要がないことが攻撃者にとってのメリットである。

### DOM Based XSS

上述の 2 パターンの脆弱性は、サーバ上で HTML を生成する際に、特定の文字列のエスケープが漏れていることが原因で発生する。  
サーバから正規の HTML が返されても、JavaScript による DOM 操作が原因で、意図しないスクリプトが実行される恐れがある。

例えば、URL のハッシュを取得してページ内に表示するコードがあるとする。

```js
div = document.getElementById('target');
div.innerHTML = location.hash.substring(1);
```

この場合、ユーザーが下記の URL にアクセスすると、悪意あるスクリプトが実行できてしまう。

```
http://example.jp/#<img src=1 onerror=alert('XSS 発生')>
```

```html
<!-- 「XSS 発生」というアラートが表示される -->
<div>
 <img src=1 onerror=alert('XSS 発生')>
</div>
```

攻撃者がコントロールできる値を **ソース**、  
スクリプトを生成・実行するために使われうる箇所を **シンク** という。

DOM Based XSS の場合、攻撃用のコードがサーバに送信されないため、攻撃のログがサーバ上に残らず、被害の特定が難しいと考えられる。

## XSS 対策

XSS においては、

- 閲覧者の Cookie の読み出し
- ページの書き換え
- 非同期通信

など、**JS でできるあらゆることが実行できてしまう** ことが大きな問題である。  
特に、セッション管理で Cookie が使われている場合にその値を盗み出されてしまうと、セッションハイジャックの危険性もある。

### エスケープ処理

`<`, `>`, `"` のような、HTML で特別な意味を持つ記号を別の記号に置き換える。  
`<script>` という文字列は `&lt;script&gt;` へと変換されるため、意図しないスクリプトの実行を防ぐ。

### Content Seucrity Policy

インラインスクリプトやインラインのイベント属性を無効化する、  
サーバから指定したホワイトリストに載っているドメインのスクリプトのみを実行する、  
といった制限を設けることができる。

サイト管理者が、すべてのコンテンツをサイト自身のドメインから取得したい場合は、HTTP レスポンスに下記のヘッダを設定する。

```
Content-Security-Policy: default-src 'self'
```

### X-XSS-Protection

ブラウザの XSS フィルターの機能を有効にする。  
ブラウザが XSS を検知した場合、ページをサニタイズしたり、レンダリングを中止するなどのアクションをとる。

XSS フィルタリングを有効にし、レンダリング中止を優先したい場合は、HTTP レスポンスに下記のヘッダを設定する。

```
X-XSS-Protection: 1; mode=block
```

### X-Content-Type-Options

ブラウザは HTTP レスポンスの `Content-Type` をみて、コンテンツをどのように処理をするか決めている。  
一部のブラウザではレスポンス全体を検査し、指定された `Content-Type` を無視して処理を行うものもある。  
この場合、JSON や CSV を HTML と誤って判断し、XSS を引き起こす恐れがある。

ブラウザ独自のコンテンツ検査を無効化したい場合は、HTTP レスポンスに下記のヘッダを設定する。

```
X-Content-Type-Options : nosniff
```

## CSRF (Cross Site Request Forgeries)

攻撃者は悪意のあるスクリプトを踏ませ、ユーザーの意図しないリクエストを送信させる（リクエスト強要）。  
アプリケーション側で「ユーザーが意図して送信した正しいものかどうか」ということを検証できない場合、データ漏洩やなりすましといった被害が発生する恐れがある。

例えば、CSRF 脆弱性をもつとあるショッピングサイトがあるとする。  
このショッピングサイトでは商品 piyo を購入する場合に、下記のリクエストを送信する必要がある。

```
POST http://example.jp/order
{ item: piyo }
```

攻撃者は自身の罠サイト上に下記の HTML を用意したうえ、SNS や掲示板といったサイトで下記のページへのアクセスを促す。

```html
<body onload="document.forms[0].submit()">
  <form method="POST" action="http://example.jp/order">
    <input type="text" name="item" value="piyo">
  </form>
</body>
```

ユーザーがこのページを開いた段階で、ショッピングサイトへのリクエスト送信が完了する。  
ユーザーがこのショッピングサイトにログイン中であれば、ショッピングサイト側はこのリクエストをユーザーからの注文要求と解釈し、不正に実行してしまう。

## CSRF 対策

トークンによる認証を行う。  
サーバ側で予測困難なトークンを生成し、クライアント側にページを返す際にそのトークンを含める。  
クライアント側からは、入力値とあわせてトークンを含めたリクエストを送信する。  
サーバ側で受け取ったトークンを検証することで、「正規のリクエストかどうか」を判断することができる。

## 参考

- [クロスサイトスクリプティング - Wikipedia](https://ja.wikipedia.org/wiki/%E3%82%AF%E3%83%AD%E3%82%B9%E3%82%B5%E3%82%A4%E3%83%88%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%97%E3%83%86%E3%82%A3%E3%83%B3%E3%82%B0)
- [クロスサイトリクエストフォージェリ - Wikipedia](https://ja.wikipedia.org/wiki/%E3%82%AF%E3%83%AD%E3%82%B9%E3%82%B5%E3%82%A4%E3%83%88%E3%83%AA%E3%82%AF%E3%82%A8%E3%82%B9%E3%83%88%E3%83%95%E3%82%A9%E3%83%BC%E3%82%B8%E3%82%A7%E3%83%AA)
- [JavaScriptセキュリティの基礎知識](http://gihyo.jp/dev/serial/01/javascript-security)
- [［はまちちゃんのセキュリティ講座］ここがキミの脆弱なところ…！](http://gihyo.jp/dev/serial/01/hamachiya2)
- [HTML5時代の「新しいセキュリティ・エチケット」](http://www.atmarkit.co.jp/ait/series/1319/)
- [RESTFulなAPIとCSRFとその対策](http://watanabe-tsuyoshi.hatenablog.com/entry/2015/03/04/123649)
- [オリジン間リソース共有 (CORS) - MDN](https://developer.mozilla.org/ja/docs/Web/HTTP/HTTP_access_control)
- [同一オリジンポリシー - MDN](https://developer.mozilla.org/ja/docs/Web/Security/Same-origin_policy)
- [CORSヘッダの設定方法について](https://community.akamai.com/groups/akamai-japan/blog/2016/07/05/cors%E3%83%98%E3%83%83%E3%83%80%E3%81%AE%E8%A8%AD%E5%AE%9A%E6%96%B9%E6%B3%95%E3%81%AB%E3%81%A4%E3%81%84%E3%81%A6)
