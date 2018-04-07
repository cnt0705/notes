# プロトタイプについて理解する

## オブジェクト指向

javascriptはオブジェクト指向言語であるが、javaやphpのようなクラスという概念はない。  
所謂プロトタイプベースのオブジェクト指向である。

クラスベースの場合、クラスという設計図(型)をもとにしてオブジェクト(実体)を量産する。  
プロトタイプベースの場合、クラス(型)の代わりにプロトタイプオブジェクト(実体)が存在する。  
個々のオブジェクトで共通なメソッドやプロパティをプロトタイプに定義することで、  
継承(のようなこと)が可能である。

## コンストラクタ

* オブジェクトの初期化に使われるもの
* 関数定義がそのままコンストラクタになる
* コンストラクタとして使用する場合には、慣習として大文字で始める
* コンストラクタは戻り値を持つことができない

## \_\_proto\_\_プロパティ

* すべてのオブジェクトが持つプロパティ
* 自身にないメソッド/プロパティが呼び出された場合に、\_\_proto\_\_が指すprototypeオブジェクトを検索する  
=> プロトタイプチェーン
* インスタンス生成時にprototypeへのアドレス情報が代入される
* Object.prototype(すべてのオブジェクトの元)の\_\_proto\_\_にはnullが入っている
* 対象のプロパティを遡って検索し、\_\_proto\_\_ = nullになったら終了、undefinedを返す

## prototypeプロパティ

* 関数オブジェクトが持つプロパティ
* プロトタイプオブジェクトの控え室
* プロトタイプにインスタンスを格納することで、継承(のようなこと)ができる

```
var Dog = function() {
  // 処理
}
Dog.prototype.color = 'white';

var Shiba = function() {
  // 処理
}
Shiba.prototype = new Dog();

var pochi = new Shiba();

// pochi自身にcolorの定義がないため、プロトタイプチェーンによりDogのprototypeまで検索される
console.log(pochi.color);   // => white

pochi.color = 'black';

// より近い場所で定義が追加されたため、そちらを優先して検索終了
console.log(pochi.color);   // => black
```

## prototypeの拡張

```
// コンストラクタ関数を定義する
var Person = function(name) {
  this.name = name;
};

// prototypeプロパティを拡張する
Person.prototype.sayHello = function() {
  alert('Hello ' + this.name);
}

// インスタンスを生成する
var person = new Person('Nicole');

// 実行
person.sayHello();  // => Hello Nicole
```

## prototypeで定義することによるメリット

**コンストラクタでプロパティを定義する場合**

```
var Human = function(name ) {
  this.name = name;
  this.say = function() {
    alert("Hi! " + name);
  };
};

var human = new Human("satoko");
human.say();    // => satoko
```

この場合は、newするたびに毎回sayという関数オブジェクトが生成される。

**プロトタイプでプロパティを定義する場合**

```
var Human = function(name ) {
  this.name = name;
};
Human.prototype = {
  say: function() {
    alert("Hi! " + name);
  }
};

var human = new Human("satoko");
human.say();    // => satoko
```

prototypeに定義することで、何度インスタンスを生成しても共通のsayを参照する。  
=> メモリ領域の節約になる。

## インスタンス

```
var obj = new Person('Nicole');
```

を実行したとき、下記の疑似コードのような意味合いがある。

```
// 新しいオブジェクトを生成する
var newObj = {};

// 参照渡し
// Personのprototypeへのアドレス情報を格納する
newObj.__proto__ = Person.prototype;

// newObjをthisとしてPerson(コンストラクタ関数)を実行する
Person.apply = (newObj, arguments);

// コンストラクタ関数によって初期化済みのオブジェクトを返す
return newObj;
```


## 参考ページ

* http://qiita.com/awakia/items/8ff451ca5f8ae0122be7
* https://speakerdeck.com/tacamy/js-girls-tokyo-number-1
* http://tacamy.hatenablog.com/entry/2012/12/17/000931
* http://blog.livedoor.jp/sasata299/archives/51189103.html
* http://d.hatena.ne.jp/tacamy/20121209/1355034499
* http://www.slideshare.net/yuka2py/javascript-23768378
* http://kagigotonet.hatenablog.com/entry/20100202/1265080352
* http://maeharin.hatenablog.com/entry/20130215/javascript_prototype_chain
* http://d.hatena.ne.jp/m-hiyama/20050908/1126154117
