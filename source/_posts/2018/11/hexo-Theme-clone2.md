---
title: hexoのThemeに使うリポジトリをcloneしてきたらデプロイ失敗する問題 その後
date: 2018-11-26 23:11:04
tags: hexo
---

[hexoのThemeに使うリポジトリをcloneしてきたらデプロイ失敗する問題](https://mick-labo.netlify.com/2018/11/26/hexo-theme-clone/)
という記事を書きましたがその後問題が発生しました。

themeの設定をするために、themesディレクトリ内の_config.ymlを修正して気が付きました。

...この修正、デプロイできないんじゃね？

前回、`themes/cactus`ディレクトリはsubmoduleで管理することになりましたが、それはつまり`themes/cactus`にプルリク送らないと修正できないということ。もしくはforkしてから修正するとか。

そこでやっと気付いたんですが、`themes/cactus`がgitで感知されてしまうのなら`.git`ディレクトリを捨てればいいんじゃないの？

というわけでやってみました。

```sh
# 前回作った.gitmodulesを捨てる
> rm .gitmodules
# themeを管理している.gitを捨てる
> rm -rf themes/cactus/.git
```
> rmコマンドは間違うとめんどくさいことになるので注意
> 
> 不安なら手動でゴミ箱へ

ここで念のために確認

```shell
> ls-files --stage | grep 160000
160000 7277cd9c5adbb04913e730c1e6848c443dbf6ebe 0       themes/cactus
```

まだ残っているのでキャッシュごと`git rm`で削除

```shell
> git rm --cached themes/cactus
```

再確認

```
> git ls-files --stage | grep 160000
```

なにも出力されなければOKです。