# npm リファレンス

## npm (Node Package Manager) とは

- Node.js 用のパッケージマネージャのこと
- bower はクライアントサイド用のライブラリを管理する

## dependencies の種類

### dependencies

ライブラリ **実行時** に必要なパッケージがインストールされる。

```json
{ 
  "name": "my-plugin",
  "dependencies": {
    "hoge": "^1.5.0"
  },
  "devDependencies": {
    "piyo": "^3.2.0"
  }
} 
```

- プラグインとして公開された `my-plugin` を開発者以外が `npm install my-plugin` する
- 上記の package.json があるディレクトリで `npm install --production` する

結果： `hoge` のみがインストールされる。

**package.json への追加方法**
```
npm install --save [package]
# npm i -S
```

### devDependencies

- テストツール
- ビルドツール
- タスクランナー

など、ライブラリ **開発時** に必要なパッケージがインストールされる。

```json
{ 
  "name": "my-plugin",
  "dependencies": {
    "hoge": "^1.5.0"
  },
  "devDependencies": {
    "piyo": "^3.2.0"
  }
} 
```

- 上記の package.json があるディレクトリで `npm install` する

結果： `hoge` および `piyo` がインストールされる。

**package.json への追加方法**
```
npm install --save-dev [package]
# npm i -D
```

## nuc の使い方

アップデートが必要なパッケージを表示する。

```
ncu
```

- アップデートが必要なパッケージについて package.json を更新する
- 更新後、`npm update` を実行してアップデートする

```
ncu -u

# パッケージ名の指定もできる
ncu -u [package]

# 特定のパッケージを除外できる
ncu -x [package]

# マイナーバージョン以下がアップデートされたパッケージも対象に含めたい場合は --upgradeAll で
ncu -a
```

## 参考

- [ちゃんと使い分けてる? dependenciesいろいろ。](https://qiita.com/cognitom/items/acc3ffcbca4c56cf2b95)
- [【いまさらですが】package.jsonのdependenciesとdevDependencies](https://qiita.com/chihiro/items/ca1529f9b3d016af53ec)
- [package.jsonのpeerDependenciesについての理解](http://waysaku.hatenablog.com/entry/2017/06/06/235234)
- [依存関係の種類 (Yarn)](https://yarnpkg.com/lang/ja/docs/dependency-types/)
