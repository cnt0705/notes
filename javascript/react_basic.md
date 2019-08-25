# React Overview

* UI を作るための JavaScript ライブラリ
* 複雑な UI を分割し、コンポーネント化する
* DOM 作成前の HTML 表現オブジェクトを React Elements と呼ぶ
* React Elements はイミュータブルオブジェクト
* `render()` は React Elements を消す
* JSX はデフォルトで HTML タグをエスケープする（XSS 回避）
* React Component はすべて Pure Function として振る舞うべき
* `componentDidMount` → React Elements が DOM にレンダリングされたあと
* `componentWillUnmount` → React Elements によって生成された DOM が消えるとき
* React は内部で非同期的に State を更新するため、現在の State に依存する処理を書きたいときは `setState` に関数を渡す
  * see: [ReactのsetStateについて](https://k4h4shi.com/2018/04/21/about-setState/)
* イベントハンドラには this が自動的にバインドされないので注意する
  * see: [ReactをES6で開発時のbindの問題](https://qiita.com/konojunya/items/fc0cfa6a56821e709065)
  * Arrow Function で定義すると、要素がレンダリングされるたびに新しい関数が定義られるので、パフォーマンス的に微妙
* コンポーネントをレンダリングしたくないときは `null` を返す
  * Lifecycle Methods は発火するけど
* Array の中のコンポーネントにはユニークな key をつける
* Value を React で管理されている form 系要素を Controlled Components と呼ぶ
  * 値は "the source of truth" として React でのみ管理する
* React では、コードの再利用性を高めるため継承よりコンポジションを選択する
* コンポーネントのタグ内に渡された JSX は `props.children` として取得・表示できる
  * 任意の props として流し込むこともできる
* React は双方向データバインディングを行わないので、情報は常に一方向で流れる

# Component

* クラスコンポーネントと関数コンポーネントがある
* クラスコンポーネントは機能が豊富で、変数（状態）を維持できるが、複雑化しやすい
* 関数コンポーネントはシンプルだが機能制限が多い
* アウトプットには差がない
* Syntax およびトランスパイル後のコードに大きな差がある
* State, Lifecycle Hook は Class Components でのみ利用できる
* Functional Components のメリット：
  * ステートレスなので読みやすくテスタブルである
  * コード量が少ない
  * Presentational Components と Container に分離しやすい
  * パフォーマンスに優れる

# State

* `setState` で state の更新を行い、コンポーネントを再描画する
* state をどこで保持するか：関連する子コンポーネントの親コンポーネントで一元管理する
* 親コンポーネントに state を管理されているコンポーネントを co ntrolled components と呼ぶ
* State は限りなくシンプルに、DRY に定義する
* State でないものの見分け方：
  * props を通じて親から値が渡されているもの
  * 時間が経っても不変なもの
  * 他の state や props の値を用いた計算から導かれるもの

# Hooks

* コンポーネントごとの変数ストレージ
* グローバルを汚染することなく、関数コンポーネントに状態を保たせる
* Custom Hooks により State を管理するロジックを使い回せるようになる
  * 従来では render props や HOC による「ラッパー地獄」で解決しており、コードが読みにくくなっていた
* Lifecycle メソッド単位ではなく、メソッドの関心単位でコンポーネントを分割できる
* Hooks は function のトップレベルで呼ぶ
  * ループ、条件文、ネストした function の中では呼ばない
  * 条件文は Hooks の中で書く
  * `useState` が呼ばれる順番が常に同じでなくてはならない
* Hooks は React function か Custom Hook の中で呼ぶ

## useState

* 状態管理のための機能
* 保持したい値と、値を更新するための関数を返す

## useCallback

* コールバック関数を保存するための機能
* 第二引数に与えた値が変更されたら関数を作り直す

## useEffect

* データフェッチ、DOM 変更といった side effects を扱う
* Lifecycle メソッドと同じ目的で利用できる
* `useEffect` 内のコードは mount のタイミングで実行される
* 返り値のメソッドは unmount のタイミングで実行される (clean up method)

## useRef

* JSX の要素にスクリプトからアクセスするための機能
