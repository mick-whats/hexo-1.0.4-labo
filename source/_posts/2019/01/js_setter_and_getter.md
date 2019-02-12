---
title: jsのgetter&setter再入門
tags:
  - js
  - getter
  - setter
---

jsのsetter&getter、かなり勘違いしていたけど理解するとなかなか使える子みたいなので覚書。

- 関数だと思ってた

## case 1

```js
test('getter', () => {
  const obj = {
    get name() {
      return 'alice';
    },
  };
  // obj.nameは`alice`が返る
  expect(obj.name).toBe('alice');
  // 代入してみる
  obj.name = 'bob';
  // 代入したはずのbobが返らない
  expect(obj.name).not.toBe('bob');
  // aliceがそのまま残ってる
  expect(obj.name).toBe('alice');
});
```

ポイントは`get name(){}`の部分です。これがgetterです。

`obj.name`はプロパティの取得に見えますが、実は`get.name()`と同義です。getterを定義すると`()`を使わずに呼べる関数になります。

上記ではsetterは定義されていません。なので`obj.name`に値を代入することはできません。(つまりreadonlyです)

`obj.name`は`alice`を返す関数なので、何度呼んでも`alice`が返ります。

## case 2

`get name()`をちょっと変えてみます。privateプロパティの`_name`を返すようにしてみました。
```js
test('getter 2', () => {
  const obj = {
    _name: 'alice',
    get name() {
      return this._name;
    },
  };
  expect(obj.name).toBe('alice');
  obj.name = 'bob';
  expect(obj.name).not.toBe('bob');
  expect(obj.name).toBe('alice');
});
```
`_name`を返しますが、相変わらずsetterは定義されていないので動作はcase1と同じです。

しかし`_name`は`なんちゃってprivate`なのでアクセスできます。
```js
// `name`ではなく`_name`に代入
obj._name = 'bob';
// getterは`_name`を返すので`bob`が返る
expect(obj.name).toBe('bob');
```

## case 3

次はsetterを定義します。

```js
test('getter & setter', () => {
  const obj = {
    _name: 'alice',
    get name() {
      return this._name;
    },
    set name(str) {
      this._name = str;
      return;
    },
  };
  expect(obj.name).toBe('alice');
  obj.name = 'bob';
  expect(obj.name).toBe('bob');
  expect(obj.name).not.toBe('alice');
});
```
期待通りの動作です。

setterの実行部分は`obj.name = 'bob';`の部分です。これは一見すると代入しているように見えますが違います。`obj.name`はプロパティではないのです。  

`obj.name = 'bob';`は`obj.name('bob')`と同義です。

```js
test('getter & setter 2', () => {
  const obj = {
    _name: 'alice',
    get name() {
      return this._name;
    },
    set name(str) {
      this._name = str;
      return;
    },
  };
  expect(obj._name).toBe('alice');
  obj._name = 'bob';
  expect(obj._name).toBe('bob');
  expect(obj._name).not.toBe('alice');
});
test('setter', () => {
  const obj = {
    names: [],
  };
  expect(obj.names).toEqual([]);
  obj.names = 'bob';
  expect(obj.names).toEqual('bob');
  obj.names = 'alice';
  expect(obj.names).toEqual('alice');
});
test('setter 2', () => {
  const obj = {
    _names: [],
    get names() {
      return this._names;
    },
    set names(str) {
      this._names.push(str);
      return;
    },
  };
  expect(obj.names).toEqual([]);
  obj.names = 'bob';
  expect(obj.names).toEqual(['bob']);
  obj.names = 'alice';
  expect(obj.names).toEqual(['bob', 'alice']);
});
```


# getter and setter

まず基本的な書き方。

```js
class Test {
  constructor(name) {
    this._name = name;
  }
  get name() {
    return this._name;
  }
  set name(name) {
    this._name = name;
  }
}
const t1 = new Test('alice');
console.log(t1);
// Test { _name: 'alice' }
```

getterは`_name`の値を返し、setterは引数`name`を`this.name`に代入する。それだけです。

ここでまず疑問になるのが`get setはどうやって呼ぶの？`って所。

- getterを呼ぶのは`name()`とする
- setterを呼ぶのは`name('hoge')`とする

そう思ってしまいがちですがこれは間違いです。正解は

## 誤解ポイントその１
- const name = t1.name // getter
- t1.name = 'hoge' // setter

getter & setterを使うときは関数のように`()`を付けてはいけません。(でも関数です)ここがややこしいポイントその１です。

## 誤解ポイントその２

先の例では`get name()`と`set name()`でインスタンスメンバーの`_name`にアクセスしています。なので`name`のgetter&setterには`_name`というプロパティを対応させないといかないのだな、と思いがちですがそうではありません。慣例的にしているだけで`_name`の代わりに`address`という名前のプロパティにしてもOKです。(ただしややこしいのでバッドノウハウです)

ややこしいポイントその２: プロパティ名を`_hoge`とするのは慣例であり拘る必要は無いが、それがベストプラクティス。

## 誤解ポイントその３

そもそも`name`というプロパティに値を出し入れするのがget&set関数だと思いがちです。なのでこんなコードを書きがちです。

```js
class Test {
  constructor(name) {
    this.name = name;
  }
  get name() {
    return this.name;
  }
  set name(name) {
    this.name = name;
  }
}
```

`_name`ではなく内部保存用プロパティも`name`にしてしまった場合です。
`get`と`set`は同じ名前で構いませんが、保存用プロパティも同じ名前にしてはいけません。この例でインスタンスを初期化すると永久ループします。

> 同一のプロパティに別の set あるいはデータエントリを持つオブジェクトリテラルが現れてはいけません。  
>（ ※ { set x(v) { }, set x(v) { } } および { x: ..., set x(v) { } } は不可 ）
>
> [setter \| MDN](https://developer.mozilla.org/ja/docs/JavaScript/Reference/Operators/set)


## そもそもgetter & setterはいらない？

ここまで**基本的な**getter & setterの解説をしました。そこで疑問になるのが「これ別にいらなくね？」ということ。  
例えばgetter & setterを使わずに書いた下記のコードは最初の例と同じ動作をします。

```js
class Test {
  constructor(name) {
    this.name = name;
  }
}
const t1 = new Test('alice');
console.log(t1);
// Test { name: 'alice' }
```

`カプセル化の為にgetter & setterを使うべき`という論調もありますが、それは一旦外してsetterを使うメリットをひとつ上げます。

setterを使うと値のバリデーションができます。  
以下の例では入力値`val`を`_values`プロパティに代入しています。このプロパティはArray型であることを期待します。

setterを定義しない場合、`hoge.values = 'val'`という風に`Arrayではない型`でも代入されてしまいますが、以下のようにすると`Arrayじゃない値を代入しようとするとArrayに変換する`という処理が行われます。
結果的に`values`プロパティには`Arrayじゃない値`は代入できなくなります。

```js
class Test {
  constructor(val) {
    this.values = val;
  }
  get values() {
    return this._values;
  }
  set values(val) {
    if (val == null) {
      this._values = [];
    } else if (Array.isArray(val)) {
      this._values = val;
    } else {
      this._values = [val];
    }
  }
}
const t1 = new Test('alice');
expect(t1.values).toEqual(['alice']);
const t2 = new Test(['alice']);
expect(t2.values).toEqual(['alice']);
const t3 = new Test();
expect(t3.values).toEqual([]);
```


最後に、上記の例でポイントをひとつ。  
constructorの中で`this.values = val;`という部分がありますが、ここでもsetterは呼ばれています。