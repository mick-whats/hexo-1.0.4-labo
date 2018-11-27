---
title: hexoのThemeに使うリポジトリをcloneしてきたらデプロイ失敗する問題
date: 2018-11-26 13:53:04
tags:
  - [hexo]
  - [theme]
  - [deploy]
---

この記事は参考程度に残していますが解決策は[こちら](https://mick-labo.netlify.com/2018/11/26/hexo-theme-clone2/)
の方がおすすめです

---

github経由でnetlifyにデプロイしようとしたら失敗しました。

原因は`git clone`したThemeです。

> [probberechts/hexo\-theme\-cactus](https://github.com/probberechts/hexo-theme-cactus)


## install時の手順

公式に書いてある通りにcloneします。

```
git clone https://github.com/probberechts/hexo-theme-cactus.git themes/cactus
```
`_config.yml`を開き、編集します。

```
# theme: landscape
theme: cactus
theme_config:
  colorscheme: white
```

この状態で`hexo server`するとローカルでは反映されましたが、netlifyにデプロイしたら失敗。

```
Error checking out submodules: fatal: No url found for submodule path 'themes/cactus' in .gitmodules
```

`'themes/cactus'`のurlが`.gitmodules`に書いてないよ的な感じですが、そもそも`.gitmodules`が存在しません。

`git submodule add`をcloneの代りに行えば良いのかと思い`themes/cactus`を削除して以下を試してみました。

```
>git submodule add https://github.com/probberechts/hexo-theme-cactus.git themes/cactus
'themes/cactus' already exists in the index
```

「もうあるじゃん」と言われて失敗。  

statusを確認してみます。


```
> git submodule status

fatal: no submodule mapping found in .gitmodules for path 'themes/cactus'
```

やっぱり`.gitmodules`をちゃんとしろって言われます。無いんだってば。

`git clone --recursive`使うと良いとか色々やってみたんですが、`.gitmodules`は作成されません。

結局手動で作ってみました。

```
[submodule "themes/cactus"]
    path = themes/cactus
    url = git://github.com/probberechts/hexo-theme-cactus.git
```

再度commit & push


```
1:34:31 PM: Build ready to start
~~
1:34:57 PM: Finished processing build request in 24.790451527s
```

成功です。

## 問題点

- リポジトリ内で`git clone`するとsubmodule扱いになる
- submoduleはgit的に別管理されているのでurlとpathを含んだ設定ファイルが必要
- submoduleが含まれてるのに設定ファイル(.gitmodules)が無いリポジトリはcloneに失敗する

