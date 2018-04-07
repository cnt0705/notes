# Web Components の基礎

- HTML, CSS, JavaScript といった Web 標準技術で、再利用できるコンポーネントを作る仕組み
- Shadow DOM, Custom Elements, Template, HTML Imports の 4 つの仕様からなる
- CSS や JavaScript のグローバルスコープ問題やインポートの仕組みの弱さを、ブラウザのレベルで解決できる

## Shadow DOM

- 任意の HTML 要素をホストとして作成される、隔離された DOM
- HTML, CSS, JavaScript の影響範囲を Shadow DOM 内に限定する
- 外部からのアクセスも限定的にする

### iframe との違い

```html
<iframe src="iframe.html" width="200" height="100"></iframe>
```

- HTML ドキュメントの中に別の HTML ドキュメントを埋め込むための要素
- 親ドキュメントがもつ CSS や JavaScript からはアクセスできない
- セキュリティが強く柔軟性に欠ける
- 小さなボタンやちょっとした UI を設置する目的としては少し大げさかも

## Custom Elements

- 独自の HTML 要素を定義する
- 任意のスタイルや振る舞いを定義できる
- カスタム要素の[ライフサイクルフック](https://developer.mozilla.org/ja/docs/Web/Web_Components/Custom_Elements)を利用する

## Templates

- テンプレートとして使いたい HTML を定義するための要素
- 従来：非活性にしたい要素に `display: none;` を付与して不可視にすればおっけー
  - 子要素に img 要素があったらリクエストが行われる…
- template 要素がアクティブになるまで、ブラウザはタグ内のコードを評価しない
- テンプレートを HTML 上に記述できるので、メンテナンスがしやすくなる

## HTML Imports

```html
<link rel="import" href="myfile.html">
```

- link 要素を用いて他の HTML ファイルを読み込むための仕様

> HTML Imports は ES Modules を見越して見送られていたが、ブラウザでの ES Modules が無事着地したことでお蔵入りになった。

[Web Components 周辺の仕様とかブラウザ互換性 2017年秋](https://1000ch.net/posts/2017/webcomponents-specs.html) より

> HTML imports の使用は、ES6 モジュールで開発者が何ができるかを確認することは、待って欲しいです

[Firefox での Web Components のサポート状況](https://developer.mozilla.org/ja/docs/Web/Web_Components/Status_in_Firefox) より

## ブラウザサポート

https://www.webcomponents.org/

## リポジトリ

https://github.com/cnt0705/web-components-playground

## 参考

- [1000ch/Web Components sandbox](https://github.com/1000ch/webcomponents-sandbox)
- [EagleLand](https://1000ch.net/)
- [The State of Web Components](https://speakerdeck.com/1000ch/the-state-of-web-components)
- [基礎からわかる Web Components 徹底解説 〜仕様から実装まで理解する〜](https://html5experts.jp/series/web-components-2/)
- [Web Componentsについて気になること、泉水さんに全部聞いてきました！](https://html5experts.jp/shumpei-shiraishi/24239/)
- [Google: Shadow DOM v1: 自己完結型ウェブ コンポーネント](https://developers.google.com/web/fundamentals/web-components/shadowdom?hl=ja)
- [HTML5 Rocks: Custom Elements](https://www.html5rocks.com/ja/tutorials/webcomponents/customelements/)
- [MDN: Custom Elements](https://developer.mozilla.org/ja/docs/Web/Web_Components/Custom_Elements)
- [GitHub](https://github.com/webcomponents)
- [webcomponents.org](https://www.webcomponents.org/introduction)
