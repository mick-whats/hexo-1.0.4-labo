---
title: jestは並列だけど直列で行われる
date: 2018-11-26 21:31:00
tags:
  - [jest]
  - [js]
---

# jestは並列だけど直列で行われる


jestは並列で実行されると聞きました。

> Jest はパフォーマンスを最大化するため、テストをワーカーにまたがり並列実行します。
> 
> <a href="https://jestjs.io/ja/">Jest · 🃏快適なJavaScriptのテスト</a>

ちょっと気になったので検証してみます。  

まずsleep的な関数を作ってみました。

```js
function async(delay) {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve();
    }, delay);
  });
}
```

3つのテストファイルを作ってそれぞれに１つだけテスト関数を書いています。  
これを実行。

```js
// parallel.01.test.js
test('parallel 01', async () => {
  await async(2000);
});

// parallel.02.test.js
test('parallel 02', async () => {
  await async(3000);
});

// parallel.03.test.js
test('parallel 03', async () => {
  await async(1000);
});
```



結果は以下。

```shell
> jest

 PASS  test/Parallel/parallel.03.test.js
  ✓ parallel 03 (1005ms)

 PASS  test/Parallel/parallel.01.test.js
  ✓ parallel 01 (2004ms)

 PASS  test/Parallel/parallel.02.test.js
  ✓ parallel 02 (3010ms)

Test Suites: 3 passed, 3 total
Tests:       3 passed, 3 total
Snapshots:   0 total
Time:        5.26s
Ran all test suites.
```

直列なら最低でも6000ミリ秒かかる所ですが5.26秒、つまり5260ミリ秒で終わっています。そしてテストが終わった順にソートされている。  
どうやら並列で行われているようです。


次は１つのファイルに３つのテストを並べてみます。  

```js
// parallel.04.test.js
test('parallel 01', async () => {
  await async(2000);
});
test('parallel 02', async () => {
  await async(3000);
});
test('parallel 03', async () => {
  await async(1000);
});
```

前回は3つのファイルに１つずつのテストでしたが、今回は１つのファイルです。  
結果はこちら。

```shell
> jest

 PASS  test/Parallel/parallel.04.test.js (6.646s)
  ✓ parallel 01 (2005ms)
  ✓ parallel 02 (3002ms)
  ✓ parallel 03 (1004ms)

Test Suites: 1 passed, 1 total
Tests:       3 passed, 3 total
Snapshots:   0 total
Time:        7.389s, estimated 8s
Ran all test suites.
```

直列で実行されてるっぽいんですが、ちょっとわかりにくいのでlog仕込んでみます。

```js
const start = Date.now();
test('parallel 01', async () => {
  console.log('01-start: ', Date.now() - start);
  await async(200);
  console.log('01- end : ', Date.now() - start);
});
test('parallel 02', async () => {
  console.log('02-start: ', Date.now() - start);
  await async(300);
  console.log('02- end : ', Date.now() - start);
});
test('parallel 03', async () => {
  console.log('03-start: ', Date.now() - start);
  await async(100);
  console.log('03- end : ', Date.now() - start);
});
```

```shell
> jest

  console.log test/Parallel/parallel.04.test.js:4
    01-start:  4

  console.log test/Parallel/parallel.04.test.js:6
    01- end :  219

  console.log test/Parallel/parallel.04.test.js:9
    02-start:  223

  console.log test/Parallel/parallel.04.test.js:11
    02- end :  529

  console.log test/Parallel/parallel.04.test.js:14
    03-start:  531

```

うん。直列で実行されてますね。

## まとめ

- jestは複数のファイルを順番にではなく同時に実行する(つまり並列)
- 単一ファイル内にある複数のテストは直列で実行される