# クロージャについて理解する

## クロージャとは

**ローカル変数を参照している関数内の関数** のこと。

```
var closure = function() {
  var counter = 0;

  // これがクロージャ
  return function() {
    counter += 1;
    console.log(counter);
  }
}

var fn = closure();

// 破棄されたはずのローカル変数counterを参照し続ける
fn();  // => 1
fn();  // => 2
fn();  // => 3
```

fnには`function() { return ++counter; }`という無名関数がセットされる。  
fnからは戻り値の関数リテラルを経由して、ローカル変数closureを参照/操作することが可能となる。


## クロージャを使わないとどうなるか

### アンチパターンその1

```
var counter = 0;

var closure = function() {
  counter += 1;
  console.log(counter);
}

closure();  // => 1
closure();  // => 2
closure();  // => 3
```
javascriptではブロックスコープを定義することができないため、  
グローバル変数を多用すると汚染されてしまうリスクが高まる。  
また、カウンターを複数作りたい場合に、グローバル変数をその数だけ用意する必要が出てくる。

### アンチパターンその2

```
var closure = {
  counter: 0,
  countUp: function() {
    this.counter += 1;
    console.log(this.counter);
  }
}

closure.countUp();  // => 1
closure.countUp();  // => 2
closure.countUp();  // => 3
```
オブジェクトのプロパティとして設定すると、関数の外側から書き換えが可能になってしまう。


## 参考ページ

* http://dqn.sakusakutto.jp/2009/01/javascript_5.html
* http://analogic.jp/closure/
* http://d.hatena.ne.jp/artgear/20120115/1326635158
* http://builder.japan.zdnet.com/script/sp_javascript-kickstart-2007/20377941/4/
* http://tacamy.hatenablog.com/entry/2012/12/31/005951
