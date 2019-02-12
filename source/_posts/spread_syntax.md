---
title: あなたが思っているスプレッド演算子はスプレッド演算子ではない(かもしれない)
tags:
  - js
  - spread_syntax
  - rest_parameters
---

スプレッド演算子(Spread syntaxまたはスプレッド構文)について再入門。


## 1. 関数呼び出し

引用元: [スプレッド構文 \- JavaScript \| MDN](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Operators/Spread_syntax)
```js
function sum(x, y, z) {
  return x + y + z;
}

const numbers = [1, 2, 3];

console.log(sum(...numbers));
// expected output: 6

console.log(sum.apply(null, numbers));
// expected output: 6
```

上記のコードから`...numbers`は`1, 2, 3`と展開していると想像できる。  
`sum([1, 2, 3])`ではなく`sum(1, 2, 3)`である。

## 2. 配列のクローンコピー

引用元: [JSのスプレッド演算子を理解する \- Qiita](https://qiita.com/akisx/items/682a4283c13fe336c547)

```js
const foo = [1, 2];

// 配列のクローン
const bar = [...foo]; // => [1, 2]

```

上記のコードを見ると`...foo`は`1, 2`と展開していると予想できる。
`1, 2`を`[](Array リテラル)`で囲っているので`[1, 2]`となる。



## 3. 関数定義

```js
const fn = (...arr) => arr.join(':');
fn(1, 2, 3);
// '1:2:3'
```

なんかおかしい。。。

## 問題点

例１では`...numbers` -> `1, 2, 3`と変換  
例２では`...foo` -> `1, 2`と変換しているのに対して  
例３では`1, 2, 3` -> `arr`に変換している。

言い方を変えると  
例１は変数`numbers`から値を取り出す -> getter  
例２は変数`foo`から値を取り出す -> getter  
例３は値`1, 2, 3`を`arr`に代入 -> setter  
となっている。

もしかして、例３はスプレッド演算子じゃないのかも？

## 解決編

例１の[MDN](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Operators/Spread_syntax)では例３の使い方は載っていない。(関数呼び出しの例はあるが関数定義の例は無い)  
例３の使い方を探してみると[Rest parameters \- JavaScript \| MDN](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Functions/rest_parameters)のページに載っている。

つまり例３は`スプレッド演算子`ではなく`レストパラメーターズ`と呼ぶらしい。
昔は`Array.prototype.slice.call(arguments, 1)`とかやってたアレです。

## まとめ

```js
const fn = (...arr1) => arr1.join(':');
const arr2 = [1, 2, 3];
fn(...arr2)
//-> '1:2:3'
```
上記の場合
- arr1は`rest_parameters`である
- arr2は`Spread_syntax`である
- arr1とarr2は全くの別物。

