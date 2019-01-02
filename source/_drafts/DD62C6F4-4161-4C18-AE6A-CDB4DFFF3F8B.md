---
title: package.jsonのfilesと.gitignoreの関係
tags:
  - npm
  - node
---


files
===

`npm publish`(自作のmoduleをnpmにアップロード)する時に、具体的にpublishするファイルを指定することができます。  
package.jsonの`files`という項目に設定します。

```json
"files": [
  "src"
],
```
上記では`src`だけnpmにpublishすると設定しています。

例えば他に`test`というディレクトリがあっても、そこに含まれるファイルはmoduleの動作には関係しません。上記の設定により`test`ディレクトリはpublishされなくなります。

package.json > files = ホワイトリストです。

.npmignore
===

package.jsonと同じ階層に`.npmignore`というファイルを置くとpublishから除外するファイルを指定できます。




> 以下、[docs\.npmjs](https://docs.npmjs.com/misc/developers)より超訳

以下のファイルは設定しなくても除外されるようになっていますので`.npmignore`に書く必要はありません

- .*.swp
- ._*
- .DS_Store
- .git
- .hg
- .npmrc
- .lock-wscript
- .svn
- .wafpickle-*
- config.gypi
- CVS
- npm-debug.log

さらに`node_modules`ディレクトリはnpmがうまく処理してくれるのでこれも書く必要はありません。

以下のファイルは除外できません。`.npmignore`に書いていても無視されます。

- package.json
- README (and its variants)
- CHANGELOG (and its variants)
- LICENSE / LICENCE


jestのディレクトリ構成
===

私がなぜこれを書こうと思ったのか。それは`jest`です。

[facebook/jest: Delightful JavaScript Testing\.](https://github.com/facebook/jest)

一般に多くのプロジェクトでは、テストファイルは`test`もしくは`spec`といディレクトリに収められています。  
しかし`jest`の推奨する構成は以下のようなもの。

```sh
bin
├── __tests__
│   └── main.test.js
└── main.js
```

この場合、`main.js`と同じ階層に`__tests__`というディレクトリがあり、`main.test.js`はその中に含まれています。

ソースコードとテストコードの位置が近いので参照が簡単になり、テストコードが読みやすくなります。

しかし弊害として、上記の`package.json > files`から`src`をホワイトリストとして公開した場合、テストファイルもパッケージに含まれてしまいます。

この問題の解決方法を探してこの記事を書いています。

やってみた解決方法
===

### その1. filesと.npmignoreの両方に書いてみた

package.jsonには
```json
"files": [
  "src"
],
```

と書き、.npmignoreには

```sh
**/__tests__/**
```

と書いて`npm publish`してみました。

結果は失敗。files設定だけが効いてignoreは無視されたようです。

### その２. filesを消して.npmignoreだけ

```sh
coverage
memo
appveyor.yml
yarn.lock
.*
**/__tests__/*.js
```

次にfilesを消して.npmignoreだけ。次のようになりました。

- `.*`によりドットファイル全部消える
- `.npmignore`も消える
- 書いたのは全部消える
- `__tests__`ディレクトリは残る
- `__tests__`ディレクトリの中身は消える

### その３





参考
===

[https://docs\.npmjs\.com/misc/developers](https://docs.npmjs.com/misc/developers)

[\.npmignore と \.gitignore の併用に関する仕様 \- Corredor](http://neos21.hatenablog.com/entry/2018/01/05/080000)

