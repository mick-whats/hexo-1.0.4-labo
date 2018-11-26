---
title: blog-management-with-git
date: 2018-11-26 11:59:35
tags:
---

# gitでblogを管理する方法

いわゆるブログサービス、有名どころでは`はてなブログ`や`アメブロ`などがあります。  
これらのサービスは運営側に管理を任せている形になります。

一方では「自分で管理をしたい」と思われる方も多く、まず名前が上がるのは[WordPress](https://ja.wordpress.com/)でしょう。  

それぞれに一長一短あり、どれが良いとは言えません。

私が求めたのは「更新が簡単」「自由度が高い」「gitで管理」ということでした。その方向で色々調べてみました。


## gitで管理

理想は「git commit & push」で更新できることです。その為にまず考えられるのが「GitHub pages」です。

[GitHub Pages](https://pages.github.com/)

[GitHub Pages を使った静的サイトの公開方法が、とても簡単になっていた ｜ Tips Note by TAM](https://www.tam-tam.co.jp/tipsnote/html_css/post11245.html)

上記のサイトにあるように、現在では簡単に自作サイトを公開できるようになっています。

昔はGitHubアカウント１つにつき１ページだったのですが、現在はリポジトリごとの作成ができるようになっています。

方法はプロジェクトルートに「docs」というディレクトリを作り、そこに入れるだけです。


## submodule問題

hexoのThemeにcactusをインストールしました。

[probberechts/hexo\-theme\-cactus](https://github.com/probberechts/hexo-theme-cactus)


install方法はgitからcloneしてきて`_config.yml`に簡単な設定を書き込むだけです。

```
git clone https://github.com/probberechts/hexo-theme-cactus.git themes/cactus
```

この状態でnetlifyにデプロイしたら失敗。
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

だから`.gitmodules`はどうしたら作ってくれるんだよっ！？

`git clone --recursive`使うと良いとか色々やってみたんですが、`.gitmodules`は作成されません。

結局手動で作ってみました。

```
[submodule "themes/cactus"]
    path = themes/cactus
    url = git://github.com/probberechts/hexo-theme-cactus.git
```

再度commit & push



```

```