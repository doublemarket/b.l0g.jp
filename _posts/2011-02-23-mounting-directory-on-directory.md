---
id: 682
title: ディレクトリをディレクトリにマウント
date: 2011-02-23T23:42:16+00:00
author: doublemarket
layout: post
guid: http://b.l0g.jp/?p=682
permalink: /dev/mounting-directory-on-directory/
categories:
  - 開発
---

Linuxで、ディスクデバイスではなく、ディレクトリをディレクトリにマウントしたい時は

```
  
mount -bind マウントしたいディレクトリ マウントポイント
  
```

fstabに書いてしまうときは以下のように書く。

```
  
マウントしたいディレクトリ マウントポイント none bind 0 0
  
```

例えば /home を /hoge/home としてマウントしたい時は

```
  
/home /hoge/home none bind 0 0
  
```

なお、マウントしたいディレクトリ(上の例では/hoge)よりも後にこの行を書かないと、マウントできないので注意。

* * *

**海外の役立つブログ記事などを人力で翻訳して公開する[Yakst](https://yakst.com/ja)というプロジェクトをやっています。よろしければそちらもどうぞ！**
