---
id: 969
title: cygwinのbashでclearを使いたい
date: 2012-03-06T14:31:01+00:00
author: doublemarket
layout: post
guid: http://b.l0g.jp/?p=969
permalink: /uncategorized/clear-on-cygwin-bash/
categories:
  - 未分類
---

<a href="http://ameblo.jp/principia-ca/entry-11147827610.html" target="_blank">masudakさんのエントリ</a> を読んで、そういえば作業用PCにCygwin入れてないなと思ってインストールしてみたら、clearコマンドがなかった。

学生時代からの癖で事あるごとに「clear;ls」と入力するのが好きな自分は、clearコマンドがないと生きていけない。

どうやらclearコマンドが欲しい場合は、ncursesを入れると使えるようになるようだ。Cygwinのsetup.exeで、「Utils → ncurses」をインストールするようにチェックを入れれば、めでたくclearできるようになる。

* * *

**海外の役立つブログ記事などを人力で翻訳して公開する[Yakst](https://yakst.com/ja)というプロジェクトをやっています。よろしければそちらもどうぞ！**
