# [Vue.js ライフサイクル](https://alligator.io/vuejs/component-lifecycle/)

## Creation (Initialization)

- コンポーネントをDOMに追加するタイミング
- SSR中にも実行される

### beforeCreate

- コンポーネント初期化時に実行される
- dataはまだリアクティブではない
- イベントはセットされていない

### created

- dataはリアクティブで、アクセス可能である
- イベントがセットされている
- テンプレートとVirtual DOMがマウント＆レンダリングされていない

## Mounting (DOM Insertion)

- レンダリングのタイミング
- SSR中には実行されない
- 初期レンダリングの直前または直後に、コンポーネントのDOMにアクセスする必要があるときに使うフック

### beforeMount

- 初期レンダリングが発生する直前、およびテンプレート関数またはレンダー関数がコンパイルされた後に実行される

### mounted

- `this.$el`を通じてDOMにアクセスできる

## Updating (Diff & Re-render)

- リアクティブプロパティが変更されたときや、再レンダリングのタイミング
- コンポーネントがいつリレンダリングされるかを知る必要があるときに使うフック

### beforeUpdate

- コンポーネントのデータ変更後、リレンダリングの直前に実行される
- レンダリング前にコンポーネントのdataを取得できる

### updated

- コンポーネントのデータ変更後、リレンダリングの後に実行される
- プロパティの変更後にDOMにアクセスする必要があるときに使う

## Destruction (Teardown)

- コンポーネントが破棄されるタイミング

### beforeDestroy

- 破棄の直前に実行される
- DOMはまだ存在している

### destroyed

- コンポーネントは破棄され、残っていない
