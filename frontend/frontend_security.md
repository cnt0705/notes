# フロントエンドのセキュリティ

## URLとオリジン

- URL：さまざまな情報を含有するデータの複合体のこと
- プロトコルスキーム：javascript:やdata:といった多様なものがある
- セキュリティ上の境界条件を定めたコードをURL基準で書くと、その構造の複雑さやブラウザごとの挙動差から、脆弱性を作りやすい
  - ⁠「ホスト名で判断」「⁠ホスト内の特定のパスで分離」といった方法ではなく、「⁠オリジン単位で境界条件を設ける」ほうが好ましい
- 特定のリソースが信用できるものなのか、あるいは第三者の用意した可能性があり信用できないものなのか、を区別して取り扱う必要がある

### オリジンとは

- スキーム＋ホスト＋ポート のこと。

```
{scheme}://{host}{:port}
```

### Same-Origin Policy

- オリジンが異なる場合に「XHRでの読み取り」「localStorageでの読み書き」などで様々な制約を課すこと
- 動作が制限されるものはXMLHttpRequest, Canvas, Web Storageなど

### Cross-Origin Resource Sharing

- オリジンを超えてリソースを共有するための統一的なルール

#### クロスオリジン通信による脆弱性4選

- 意図せずクロスオリジン通信してXSS
  - 通信先が安全かどうかを確実にするべき
- 意図せずクロスオリジン通信してデータ漏洩
  - `Access-Control-Allow-Origin:*`は攻撃者に対しても開かれてしまうので危険
  - 攻撃者はtelnet等で任意のOriginを送信可能なので、`Access-Control-Allow-Origin`のホワイトリストのみで認証を済ませるのは危険
  - 認証は`Access-Control-Allow-Origin`以外の手法で確実に行う
- Ajaxデータを悪用してXSS
- Ajaxデータ内の機密情報漏洩
  - `X-Content-Type-Options`を正しく設定するべき

### locationオブジェクト

- 現在表示しているドキュメントのURLが格納されている
- `document.documentURI`は認証情報を含んだURLを取得できる
- URLを自力でパースして検査するというアプローチでは、URLの仕様・各ブラウザごとの挙動の差をすべて把握しなければならない
- URLコンストラクタを呼び出すことで、locationオブジェクトと同じインタフェースを持つURLオブジェクトを生成できるので、それを使うべき

## CSRF

- 攻撃者の用意した罠ページを起点とし、ユーザー自身に攻撃対象となるサイトへのリクエストを発行させることによって、ユーザー自身が意図していない処理を行わせる
- 攻撃者自身が対象サーバへ直接アクセスすることなく攻撃することができる

### 被害

- いたずら的書き込み、不正サイトへの誘導、犯罪予告といった掲示板やアンケートフォームへの不正な書き込み
- 不正な書き込みを大量に行うことによるDoS攻撃

### 例

- 攻撃用のフォームに訪問した瞬間にJavaScriptを走らせ、別ドメインのサイトに意図しないリクエストを送る

### 対策

⚠️⚠️⚠️

## XSS

- 動的なWebページの脆弱性を利用した攻撃
- Webページ表示内容生成の際に任意のスクリプトがまぎれ込み、サイト閲覧者の環境で不正スクリプトが実行される

### 被害

⚠️⚠️⚠️

### XSSいろいろ

#### 反射型XSS

- 罠ページの訪問や罠リンクのクリックにより、攻撃者の用意したJavaScriptコードやHTML断片がユーザーからのHTTPリクエストに含まれてサーバへ送信され、サーバからのレスポンスにそのJavaScriptコードやHTML断片が含まれて発生する
- リクエストとレスポンスの双方に同じJavaScriptコードやHTML断片が含まれていないかを監視し、もし同一の文字列が含まれている場合にそれらのJavaScript等の実行を遮断するブラウザの機能を、XSSフィルターという

##### 例

- フォームの確認画面

#### 蓄積型XSS

- 攻撃者の用意したJavaScriptコードやHTML断片が対象となるWebサーバ上にいったん格納され、ユーザがそのページを開いた際にそのJavaScriptコードやHTML断片が含まれて発生するタイプ

#### DOM-based XSS

- サーバ上でのHTMLの生成時には問題はなく、ブラウザ上で動作するJavaScript上のコードに問題があるために発生するタイプ

##### 例

- [攻撃シナリオ例](https://www.ark-web.jp/blog/archives/2007/02/dom_based_xss.html)
- URLのハッシュを取得して画面表示する
- フォームの入力値をモーダルで表示する

### 対策

#### [Content Security Policy](https://gist.github.com/cnt0705/c8040e716a9c5749ddfa12aadb65ebbb#第2回-cspの仕様と利用)

#### X-XSS-Protection

- HTTPレスポンスヘッダに`X-XSS-Protection "1; mode=block";`を指定する
- ブラウザのXSSフィルターの機能を有効にし、XSS攻撃を防ぐ（反射型XSS対策）
- まれに誤検知がある…らしい
  - あまりにクレームが相次ぐ場合は、XSSの危険性がないかを確実に調査し、そのページのみ`X-XSS-Protection:0`を指定するケースもあり

#### X-Content-Type-Options

- ブラウザは本来、HTTPレスポンスの`Content-Type`をみてどのように処理をするか決めている
- しかし、ブラウザが勝手にレスポンス全体を検査して`Content-Type`を判断し、本来指定されている`Content-Type`を無視して処理を行う場合がある
- 実装者の意図しない動作を引き起こし、セキュリティ上の問題が発生する可能性がある
- HTTPレスポンスヘッダに`X-Content-Type-Options : nosniff`を指定することで、ブラウザ独自の検査を行わせない
- HTMLではないものをHTMLとして扱わないようにすることで、JSONやCSVによるXSSを防ぐ

#### X-Requested-With

- XHRでやり取りされるデータをXHRからのリクエストの時のみ応答し、ブラウザから直接開かれた場合には応答しないようにする
- Content-Typeを無視して発生するXSSを防ぐ

#### DOM-based XSSを防ぐための予防

- innerHTMLやdocument.writeのような「文字列からHTML要素を生成してしまう機能」ではなく、「テキストノードとして取り扱う」「setAttributeのように対象を限定して適切に操作を行えるAPIを選択する」といった手法をとる
- リンクを作成するときは、httpまたはhttpsのスキームのみを許容するようにする
- JavaScriptのフレームワークは常に最新のものを使う
- setTimeout/setIntervalの引数に文字列を渡すとJavaScriptとして動作させることが可能であるため、引数には関数を渡す
- DOMParser APIやcreateHTMLDocumentを使用してHTML文字列をパースし、それにより構築されたDOMツリーから許可されたタグと属性のみを取り出す
  - ブラウザが現在表示しているdocumentに影響を与えることなく、与えられた文字列をパースして、DOMツリーを構築することができる

## [クリックジャッキング](https://gist.github.com/cnt0705/c8040e716a9c5749ddfa12aadb65ebbb#第3回-iframeの危険性と対策)

### 対策

#### X-Frame-Options

- ブラウザがページを`<frame>`または`<iframe>`の内部に表示することを許可するかどうか、を設定できる
- 自分のサイトのコンテンツが、他のサイトに埋め込まれないことを保証するもの
- HTTPレスポンスヘッダに`X-Frame-Options SAMEORIGIN;`を指定すると、同一サイト内での読み込み以外を拒否することができる

## その他

### X-Dowmliad-Options

- IE8以降でダウンロード時の「開く」ボタンを非表示にできる
- 添付ファイルによる蓄積型XSSを予防することができるなどの効果がある

### target="_blank" のセキュリティリスク

- https://reisato.plala.jp/rsato/weblog/2016/07/19/0215.html
- https://blog.jxck.io/entries/2016-06-12/noopener.html

## 参考

- [MDN](https://developer.mozilla.org/ja/docs/Web/HTTP)
- [脅威と対策 トレンドマイクロ](http://www.trendmicro.co.jp/jp/security-intelligence/threat-solution/index.html)
- [フロントエンドエンジニアなら知っておきたい「HTML5のセキュリティ問題と対策技術」第44回HTML5とか勉強会レポート](https://codeiq.jp/magazine/2014/02/5736/)
- [セキュリティを強化する7つの便利なHTTPヘッダ](https://devcentral.f5.com/articles/7http)
- [本当にあったフロントエンドセキュリティ怖い話](http://0-9.sakura.ne.jp/pub/lt/mini/20130919/frontend-security.html)
- [JavaScriptセキュリティの基礎知識](http://gihyo.jp/dev/serial/01/javascript-security)
