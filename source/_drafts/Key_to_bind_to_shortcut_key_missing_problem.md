---
title: ショートカットにバインドするキー足りない問題
tags:
  - macos
  - Karabiner-Elements
---

> これはmacのshortcutに関する記事です。他のOSは考慮していません。

Capslockキーが便利になるというKarabiner-Elements拡張をinstallしてみた。

[Vonng/Capslock: Make Capslock Great Again\!](https://github.com/Vonng/Capslock)



私が愛用するエディタの`vscode`は便利な拡張機能を自由に選んでインストールすることができます。
そして便利機能をショートカットに割り当てて使うことができます。

ここで問題になるのがショートカットキー足りない問題です。

単独で`cmd+〇〇`や`ctrl+〇〇`はほぼ使われているのでそこには割り当てられません。使われていないキーを見つけても他のアプリケーションと重複したりすることがあります。

`ctrl+opt+cmd+〇〇`等の`3キー同時押し`、`4キー同時押し`で割り当てられるキーは増えますが、今度は`複数同時押し覚えきれない問題`とに直面します。

できれば同時押しは2キーで新しいショートカットを割り当てたいのです。

---

キーボードショートカットを使う基本として修飾キーとの組み合わせがあります。

- `⌃ ctrl`
- `⌥ option`
- `⇧ shift`
- `⌘ cmd`

例えば`cmd+a`は全てを選択、`ctrl+a`は行頭に移動のショートカットです。

一方で`caps lock ⇪`というキーがあります。shiftキーを固定する為のキーです。しかしこのcapslockというキーは使用用途が限定的で特に便利でもないので一般的に使われていません。
そのため、ctrlキーと入れ替えたりescキーと入れ替えたりされることが多いようです。


そこで[Vonng/Capslock](https://github.com/Vonng/Capslock)の登場です。

## [Vonng/Capslock](https://github.com/Vonng/Capslock)とは

- [Karabiner-Elements](https://pqrs.org/osx/karabiner/index.html)の設定ファイル
- `caps lock ⇪`単独で`esc`キーになる
- `caps lock ⇪`に`⌃⌥⇧⌘`を割り当て
- `caps lock ⇪`+`hjkl`で移動
- 他にもいろいろ

基本となるのは修飾キーの機能です。

例えば`caps+a(2キー同時押し)`をすると内部では`⌃⌥⇧⌘+a(5キー同時押し)`となります。

これを利用してvscodeで`⌃⌥⇧⌘a`と設定すると、被らないキーバインドが実現できます。

ここから主な機能を紹介します。([このページ](https://github.com/Vonng/Capslock)の訳です)

## basic
`Capslock`を押した時の機能
| origin   | map    | comment                           |
| -------- | ------ | --------------------------------- |
| 押す     | escape | 単独押しでescape                  |
| ホールド | hyper  | 他のキーを同時に押すとhyperモード |

## Navigation

hjklで上下左右に移動できる他、ホームポジションから手を離さずに操作できる。  
マウス移動は使いにくく必要じゃないかも。

全て`caps lock`同時押し

| origin     | map                | comment          |
| ---------- | ------------------ | ---------------- |
| hjkl       | ←↓↑→               | 移動             |
| cmd + hjkl | shift + ←↓↑→       | 選択して移動     |
| u          | page up            | １画面上へ移動   |
| i          | home               | 最上部へ移動     |
| o          | end                | 最下部へ移動     |
| p          | page down          | １画面下へ移動   |
| opt + hjkl |                    | マウス移動       |
| shift + hl | ctrl+ (sihft+) tab | タブ移動         |
| shift + jk | cmd+ (shift+) tab  | アプリ切り替え   |
| ctrl + hl  | ctrl+ ← ( → )      | デスクトップ移動 |
| ctrl + k   | ctrl + ↑           | Mission control  |
| ctrl + j   | ctrl + ↓           | app window       |

## Mousekey
使わなさそうなので除外

## Deletion
遠いdeleteキーに指を伸ばさずに削除できる

| origin  | map                          | comment          |
| ------- | ---------------------------- | ---------------- |
| n       | Option + ForwardDelete       | 前の単語まで削除 |               |
| m       | ForwardDelete                | １文字削除       |               |
| ,       | Delete                       | 次の１文字削除   |               |
| .       | Option + Delete              | 次の単語まで削除 |               |
| cmd + m | Command+Option+ForwardDelete | 行頭まで削除     | cmd + nも同じ |

## Window Control
+tabで切り替えなど、ほぼcmdで使える機能の上書き。  
+qでquitするのが怖いので除外

## Bash Control
ctrlキーでやればすむことなので除外

## Applications

アプリ切り替え。地味に便利。使ってないappは書き換えれば有効活用できそう。  
(表は公式からそのまま引用)

| Origin | Maps to                 | Comment                                         |
| ------ | ----------------------- | ----------------------------------------------- |
| E      | Open Safari             | Open Web Browser                                |
| ⌘E     | Open Finder             | Open File Browser                               |
| R      | Open iTerm2             | Great terminal for osx (Run)                    |
| ⌘R     | Open Preview            | Switch to opened picture, pdf, etc...           |
| T      | Open Visual Studio Code | Text Editor: Visual Studio Code                 |
| ⌘T     | Open Typora             | Text Editor: Typora , a great WYSIWYG md editor |
| ⌘D     | Open Dictionary         | Find words                                      |
| ⌘F     | Open Dash               | Find API Document                               |
| F      | Open Alfred             | Leaves to ⌃⌥⇧⌘F                                 |
| ⌘D     | Open Dictionary         | Find words                                      |
| G      | Open Intellij           | IDEA	Open IDE                                   |
| ⌘G     | Open Chrome             | Google Chrome                                   |

## Functional
fnキーを代用できる。これを活用すればfnキーは完全にいらない子なので、fnキーをenterとして使う等の運用が可能。


## Shifter
shiftキーの代用だが`1->!` `2->@`という風に使うだけで当然小文字から大文字のshiftはできない。いらないと思う。