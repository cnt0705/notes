# Vagrantの基礎知識

## Vagrantとは

仮想マシンを簡単に立ち上げるためのツール。  
設定を一度行えば、同じ環境を何度でもすぐに作ることができる。

## Vagrantの始め方

1. Boxを取得する。
2. 仮想マシンを初期化する。
3. 仮想マシンを立ち上げる。

## Boxとは

Vagrantでは、アプリケーションやシステム開発のバックエンドを簡単にパッケージ化して共有できる。  
このパッケージ化された環境を「box」と呼ばれる単位で管理する。一つのboxからいくつも仮想マシンを作ることができる。  
boxは下記にキャッシュされる。
```
$ cd ./.vagrant.d/boxes
$ ls
```

## 仮想マシンを作る

下記コマンドでVagrantfileを生成する。
```
# 仮想マシンを作りたいディレクトリに移動
$ cd myCentOSVM/

# 使用するboxを入力してinit
$ vagrant init centos64
```

Vagrantfileがあるディレクトリで下記のコマンドを入力すると、仮想マシンがVirtualBox上に作られる。
```
$ vagrant up
```

## 各コマンド

<dl>
<dt>status</dt>
<dd>仮想マシンの状態を見る。</dd>
<dt>suspend - resume</dt>
<dd>一時停止 - 再開</dd>
<dt>halt - up</dt>
<dd>切断 - 再開</dd>
<dt>reload</dt>
<dd>仮想マシンを再起動する。</dd>
<dt>destroy</dt>
<dd>仮想マシンを削除する。（Vagrantfile自体は残る）</dd>
</dl>

## 仮想マシンに接続する

パスワードなしでSSH接続ができる。
```
$ vagrant ssh
```

## Webページを見る

Vagrantfileの設定を書き換えることで、[192.168.33.10]でブラウザからWebページを見ることができる。  
（CentOSの場合、ゲストOSのドキュメントルートは/var/www/html）

## 共有フォルダを使う

ホストOS側でVagrantfileがあるディレクトリは、ゲストOS側の下記のディレクトリとリンクしている。
```
$ vagrant ssh
$ cd /vagrant
```

仮想マシン内のドキュメントルートにシンボリックリンクを貼ると、  
ホストOS内で編集したファイルが即座にWebページに反映される。
```
$ sudo ln -fs /vagrant /var/www/html
```

## Provisioning

Provisioningとは、`vagrant up`をした後に自動的に実行される一連の処理のこと。  
Vagrantfileに直接処理を書くこともできるが、shellを別ファイルに書いてそれを読み込むこともできる。

仮想マシンが立ち上がっているときにprovisionだけ実行したい場合は、  
`vagrant provision`で実行可能。

## プラグインを導入する

下記コマンドでプラグインのインストールが可能。
```
$ vagrant plugin install [plugin name]
```
