# フロントエンドのユニットテスト

## テストの種類

### ホワイトボックステスト

- ソフトウェアの内部的な仕組みに対してテストをする
- （自動化）単体テスト
- 想定される入力値をすべてテストする
- コードが仕様通りかどうかを担保するもの
- 自動化テストは楽だけど、テスト自体にバグが潜んでいる可能性もある
- ソフトウェアの仕様変更に伴って、テストも修正する必要がある

### ブラックボックステスト

- ソフトウェアを外からみたときの振る舞いをテストする
- 結合テスト
- 完成物がシステム要件を満たしているかどうか、そもそもの仕様自体に誤りや漏れがないかを確認するもの

## ユニットテスト

### ユニットテストをなぜやるか

- ある機能を作り、動作が正しく行われたことを確認したとする
- その機能を修正するたびに、これまで正しく行われていた動作を手動で行い、結果を確認するのは工数がかかるし、非効率的…
- しかし、動作確認を怠ったり漏れがあったりすると、**コード修正後においても、期待される動作が正しく行われている**という保証がない
- つまり、デグレの危険性があり、機能追加やリファクタリングをするのが困難になっていく
- そこで、期待される動作をコードとして書いておいて、機能を修正するたびに自動で結果を確認できるようにする！

### アサーションライブラリ

- 値が正しいかどうか（A === B ?）を検証するためのメソッドを提供する
- 例えば：`equal()`,`should()`で値の比較ができる
- expect.js / should.js / chai / power-assert ...

### テストフレームワーク

- テストコードを書くためのルールや枠組みを提供する
- 例えば：テストスイートを`describe`、テストメソッドを`it`で表現する
- Mocha / Jasmine ...

### テストランナー

- テストフレームワークと組み合わせて、様々なブラウザ環境でテストを実行できるようにする
- 例えば：テスト結果をコンソール上に見やすくまとめてレポートできる
- Karma / testem ...

### テストダブル

- テスト対象が依存しているモジュールやメソッドなどを、擬似的に再現するための機能
- Sinon.js

#### Spy

- メソッドが呼ばれたときの引数や戻り値、エラーなどを監視することができる

```js
describe('Spy test', () => {
  if('Spy test', () => {
    const object = { method: str => { return str; } };

    // spyを作成する
    cosnt spy = sinon.spy(object, "method");

    // コールバックが呼ばれた回数
    expect(spy.callCount).to.be(1);

    // コールバックの引数
    expect(spy.args[0][0]).to.be('hoge');
  })
})
```

#### Stub

- 特定のメソッドの振る舞いを上書きし、自由に設定することができる

```js
describe('Stub test', () => {
  if('Stub test', () => {
    const object = { method: str => { return str; } };

    // stubを作成する
    cosnt stub = sinon.stub(object, "method");

    // 引数arg01でtrueを返すように設定する
    stub.withArgs('arg01').returns(true);
    expect(object.method('arg01')).to.be(true);
  })
})
```

#### Mock

- 「メソッドが何回呼び出されるか」「どんな引数で呼び出されるか」といった期待値を設定することができる

```js
describe('Mock test', () => {
  if('Mock test', () => {
    const object = { method: str => { return str; } };

    // mockを作成する
    cosnt mock = sinon.mock(object);

    // methodが1度だけ呼ばれることを期待する
    mock.expects('method').once();

    object.method();

    // 期待通り呼ばれたかをチェックする
    mock.verify();
  })
})
```

### テストをしやすいメソッドとは

- 機能をひとつに絞る
- 条件分岐を減らす
- 返り値をつける
- 毎回同じ結果を返す
- 他の関数に依存しない
- 再利用性が高い

テストケースを考えながら実装すると、自然とメンテナブルなコードになっていく。

### テストはいつ書くべきか

#### 機能を実装した直後

- 機能実装後に、期待する動作のテストケースを書くパターン
- すでに機能ができているのでテストが書きやすい

#### 機能を実装する前

- いわゆるTDD（テスト駆動開発）
- テストを最初に書く→テストの条件を満たす最低限の実装を行う→テストが通ったらリファクタリングを繰り返しながら開発する

#### バグを発見したとき

- バグを再現するテストコードを書き、テストが通るようにコードを修正する
- 他人にバグを報告する際、テストコードがあると意思疎通が楽になる

### テストを書く際に気をつけるべきこと

テストが実装依存にならないようにする。  
例えば：ダイアログでOKを押下後、`#userName`のテキストを空にするメソッドをテストしたいとき…

```js
const Obj = {
  deleteElement: () => {
    cosnt el = document.getElementById('userName');
    el.textContent ='';
  }
}

init() {
  if (window.confirm('内容を削除しますか？')) {
    Obj.deleteElement();
  }
}
```

**悪い例**

```js
describe('deleteElement()', () => {
  beforeEach(() => {
    stub = sinon.stub(window, 'confirm');
    spy = sinon.spy(Obj 'deleteElement');
  });

  if('deleteElement()が呼ばれたかどうか', () => {
    stub.returns(true);
    init();

    expect(spy.calledOnce).to.ok();
  })
})
```

- 「`deleteElement()`が呼ばれたかどうか」でもテストができるが、コードに依存したテストになってしまっている
- テキストを空にする処理を`deleteElement()`から別のメソッドに変更したい場合に、テストも書き直す必要が出てきてしまう

**良い例**

```js
describe('deleteElement()', () => {
  beforeEach(() => {
    stub = sinon.stub(window, 'confirm');
  });

  if('テキストが空になっているかどうか', () => {
    stub.returns(true);
    init();

    expect(document.getElementById('userName')).to.be.empty;
  })
})
```

- 「テキストが空になっているかどうか」という、外部から見た振る舞い基準でのテストになっている（BDD）
- 振る舞いを変えずにリファクタリングしたいときも、このままテストすることができる

## 参考

- [フロントエンドにテストを導入](http://qiita.com/howdy39/items/cdd5b252096f5a2fa438)
- [一から始めるJavaScriptユニットテスト](http://developer.hatenastaff.com/entry/2016/12/05/102351)
- [ES2015時代のJavaScriptテストツールまとめ](http://dackdive.hateblo.jp/entry/2016/06/26/192248)
- [ユニットテストって何?って人向けのmochaとchaiの使い方](http://qiita.com/y_hokkey/items/f73ea6b3d5f6902396b6)
- [Web制作者視点で理解するソフトウェアテスト](http://media-massage.net/works/docs/learning_software_test_from_web_production_side/)
- [経験0の経験0による経験0のためのテスト啓蒙](http://qiita.com/SpicyCoffee/items/2bb90f47ed81db02e694)
- [[CodeGrid] JavaScript開発のためのテスト入門](https://app.codegrid.net/series/js-test)
- [[CodeGrid] 実践、ユニットテスト](https://app.codegrid.net/series/2016-unit-test)
- [[Developers.IO] ユニットテストにまつわる10の勘違い](http://dev.classmethod.jp/testing/10_errors_about_unit_testing/)
- [[Developers.IO] ユニットテスト改善ガイド](http://dev.classmethod.jp/testing/unittesting/working-effectively-with-unit-testing/)
- [テスタビリティの高いコードとその書き方](https://catcher-in-the-tech.net/2356/)
