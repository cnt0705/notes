# 非同期処理の基礎知識

## キューとスタック

- キューは末尾に入れて、先頭から取り出す
- スタックは先頭に入れて、先頭から取り出す

## JavaScriptの仕様

- シングルスレッド
- キューに積まれた関数を上から順番に処理する
- ある関数Aの引数に別の関数Bを渡し、AからBを呼び出すことをコールバックという
- 非同期処理＝実行の準備ができたコールバック関数が独立してキューに積まれる

## 非同期処理の仕組み

- ある関数が呼び出されたとき、戻り値として本来渡したい結果を返すのではなく、一度関数としては終了する
- 後で「本来渡したかった値」を返せる状態になったときに、呼び出し元にその値を通知する

## setTimeout()の落とし穴

- 「指定秒後にそれに関数を実行する」のではなく、「指定秒後にそれに関数を登録している」ということ
- コールバック関数の実行タイミングは「指定時間が経ち、かつ、実行中およびキューの先にいる他の関数の処理が終了していること」
- 指定秒が経ったとしても、実行中の関数やキューの先の関数に重い処理のものがあれば、その分実行タイミングは遅くなる

```
console.log(1);
setTimeout(function() {console.log(2);}, 1000);
console.log(3);

// 結果
1
3
2
```

**同期処理**

- log(1),setTimeout,log(3)の順番にキューに登録される
- ここでのsetTimeoutは「log(2)をタイマーにセットする」だけの処理である

**非同期処理**

- タイマーにlog(2)が登録されてから1000ms後に、log(2)がキューに登録される
- ちなみに、待ち時間を0mとしても結果は変わらない

```
console.log(1);
setTimeout(function() {console.log(2);}, 0);
console.log(3);
```

## 非同期処理を実装するには

### 自前の非同期関数を実装する

`setTimeout()`を使用し、上述の方法で非同期処理を実装する。

### Promiseを使用する

- 非同期処理を監視するためのオブジェクト
- 呼び出しに応じ、将来返すはずの未知の値の代わりに、Promiseオブジェクトをその場での戻り値としておくことができる
- 結果が分かったタイミングで、Promiseオブジェクトを介して本来の値を返す
- コールバック地獄を回避して可読性の高い記述が可能とな。
- 下記の例の場合、setTimeout関数の中でvalueが定義してあればresolve関数を、未定義であればreject関数をそれぞれ呼び出す

```
function hoge(value) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if(value) {
        resolve(value);
      } else {
        reject('ERROR!');
      }
    }, 1000);
  })
}
hoge('abcde').then(
  response => { console.log(response); },
  error => { console.log(error); }
);
```

- コンストラクタには非同期処理本体を記述する

```
new Promise((resolve, reject) => {statement})

// resolve: 処理の成功を通知するための関数
// reject: 処理の失敗を通知するための関数
// statement: 処理本体
```

- Promiseオブジェクトでの処理結果を受け取るのはthenメソッド
- 引数success/failureは、それぞれresolve/reject関数で指定された引数を受け取る

```
then(success, failure)
// success: 成功コールバック関数
// failure: 失敗コールバック関数
```

- thenメソッドの配下で新たなPromiseオブジェクトを返せば、連結することもできる

```
hoge('fghij')
  .then(
    response => {
      console.log(response);
      return hoge('klmno');
    }
  )
  .then(
    response => {
      console.log(response);
    },
    error => {
      console.log(error);  // 最初のthenで失敗した際もここのerrorが呼ばれる。
    }
  );
```

## 参考URL

- [ユーザーの体感速度を高めるためのJavaScriptチューニング（後編）](https://html5experts.jp/yoshikawa_t/1016/)
- [JavaScriptはシングルスレッド：非同期処理の仕組み](http://site.oukasei.com/?p=193)
- [JavaScriptはシングルスレッドだとようやく知ったのでメモ](http://chuckwebtips.hatenablog.com/entry/2015/08/19/070000)
- [JavaScriptの同期、非同期、コールバック、プロミス辺りを整理してみる](http://qiita.com/YoshikiNakamura/items/732ded26c85a7f771a27)
- [非同期処理ってどういうこと？JavaScriptで一から学ぶ](http://qiita.com/kiyodori/items/da434d169755cbb20447)
- [今更だけどPromise入門](http://qiita.com/koki_cheese/items/c559da338a3d307c9d88)
- [今さら始めるJavaScript Promiseの基礎の基礎](http://qiita.com/chuck0523/items/db94c2faa9147c74922d)
- [俺たちはJavaScriptの非同期処理とどう付き合っていけば良いのだろうか](http://qiita.com/keitarou/items/79a038a29e1f8e39573b)
