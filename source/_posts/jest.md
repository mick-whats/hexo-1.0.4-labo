---
title: jest
date: 2018-11-25 23:38:43
tags:
---

# jest

jestのtest

generate前

```
  65B 11 25 23:36 .gitignore
 1.7K 11 25 23:36 _config.yml
 174B 11 25 23:38 db.json
  11K 11 25 23:36 node_modules/
 447B 11 25 23:38 package.json
 170B 11 25 23:36 scaffolds/
 102B 11 25 23:36 source/
 102B 11 25 23:36 themes/
  75K 11 25 23:36 yarn.lock
```

generate後

```
  65B 11 25 23:36 .gitignore
 1.7K 11 25 23:36 _config.yml
  25K 11 25 23:40 db.json
  11K 11 25 23:36 node_modules/
 447B 11 25 23:38 package.json
 272B 11 25 23:40 public/
 170B 11 25 23:36 scaffolds/
 102B 11 25 23:36 source/
 102B 11 25 23:36 themes/
  75K 11 25 23:36 yarn.lock
```

public/　が増えている

```
>la public
102B 11 25 23:40 2018/
136B 11 25 23:40 archives/
170B 11 25 23:40 css/
408B 11 25 23:40 fancybox/
7.5K 11 25 23:40 index.html
102B 11 25 23:40 js/
```

index.htmlを開いてみる -> css効いてない

