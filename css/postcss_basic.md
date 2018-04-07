# PostCSSの基礎知識

## PostCSSとは

JavaScriptで書いたプラグインによってCSSを変換するツール。  
SassやLess等のプリプロセッサと違い、必要なプラグインのみ導入することができる。  
PostCSS自体は、CSSファイルをJavaScriptのオブジェクトやトークンに解析するだけで、単体では何もできない。

## 使うには

Sass同様、タスクランナーが必要となる。

## プラグイン

現時点で200以上のプラグインがある。一例は下記の通り。  
プラグインは[PostCSS.parts](http://postcss.parts/)にてキーワードで探すことができる。

<dl>
<dt>precss</dt>
<dd>Sassライクな記述ができる。</dd>

<dt>cssnext</dt>
<dd>Autoprefixerを含み、最新CSSプリフィックスを利用可能にする。</dd>

<dt>cssnano</dt>
<dd>cssをミニファイする。</dd>
</dl>

## PostCSSを使うメリット

* 必要なプラグインのみ選定できるため、ワンパッケージで提供されるプリプロセッサに比べてコンパイルが速い。
* ワンパッケージのプリプロセッサに比べて流行り廃りがないので、メンテナンスされなくなる心配が少ない。
* プラグインは常に開発されているので、最新の技術を取り込みやすい。

## 参考

* [噂のPostCSSを検証してみた](http://creator.dwango.co.jp/5146.html)
* [PostCSSとは何か](https://speakerdeck.com/jmblog/postcss-tohahe-ka)
