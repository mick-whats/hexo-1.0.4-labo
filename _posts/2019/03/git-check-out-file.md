---
title: gitで別ブランチから特定のファイルを取り込む方法
tags:
  - git
---

## 方法

```shell
git checkout ブランチ名 -- ファイル名
```

## 解説

`git checkout`はブランチ切り替えだけのコマンドではありません。

> git checkout の機能  
> 1. 作業ブランチを切り替える  
> 2. 指定したコミット（もしくはインデックス）の状態を、現在の作業ツリーに展開する  
> 
> もしパラメータに「ブランチ名」を指定すれば、[1]の動作、ファイルパスやファイル名を指定すれば、[2]の動作となります。この２つは異なる機能ですので、注意が必要です。
> 
> [git checkout でブランチ切り替え。仕様とオプションまとめ \| WWWクリエイターズ](http://www-creators.com/archives/1388)

## 確認
実際に試してみました。


```sh
> mkdir git_checkout_test
> cd git_checkout_test
> git init
```

プロジェクト内に2つのファイルを作成します。

```js
// list.js
module.exports = ['Alexander', 'Beckham', 'Cameron'];
```

```js
// regulations.js
module.exports.version = 1.0.0
```

この状態で一旦commitします。  
新規に`jp`というブランチを作成して移動します。

```sh
> git checkout -b jp
```

jpブランチ内の各ファイルを更新します

```js
// list.js
module.exports = ['tanaka', 'satou', 'suzuki'];
```

```js
// regulations.js
module.exports.version = 2.0.0
```

masterブランチに戻ります。

```sh
> git checkout master
```

この時点でmasterブランチとjpブランチの内容は２ファイルとも異なっていますが、`list.js`を変更させずに`regulations.js`だけを取り込みたい、という必要が発生しました。

`git merge`すると`list.js`も更新されてしまいますがそれは困ります。

そこで冒頭のコマンドを打ちます。

```sh
git checkout jp -- regulations.js
```

これでjpブランチから`regulations.js`だけを取り込むことができました。

注意点として、この時点で取り込んだ`regulations.js`はステージングされていますのでaddせずにcommitしてください

## まとめ

`git checkout`はブランチ切り替えだけのコマンドでは無く、単一ファイルの取り込みもできる。
