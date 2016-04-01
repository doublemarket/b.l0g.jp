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
<div class='wp_social_bookmarking_light'>
  <div class="wsbl_hatena_button">
    <a href="http://b.hatena.ne.jp/entry/http://b.l0g.jp/uncategorized/clear-on-cygwin-bash/" class="hatena-bookmark-button" data-hatena-bookmark-title="cygwinのbashでclearを使いたい" data-hatena-bookmark-layout="standard" title="このエントリーをはてなブックマークに追加"> <img src="//b.hatena.ne.jp/images/entry-button/button-only@2x.png" alt="このエントリーをはてなブックマークに追加" width="20" height="20" style="border: none;" /></a>
  </div>
  
  <div class="wsbl_facebook_like">
    <div id="fb-root">
    </div><fb:like href="http://b.l0g.jp/uncategorized/clear-on-cygwin-bash/" layout="button_count" action="like" width="100" share="false" show_faces="false" ></fb:like>
  </div>
  
  <div class="wsbl_twitter">
    <a href="https://twitter.com/share" class="twitter-share-button"{count} data-url="http://b.l0g.jp/uncategorized/clear-on-cygwin-bash/" data-text="cygwinのbashでclearを使いたい" data-via="dblmkt " data-lang="ja">Tweet</a>
  </div>
  
  <div class="wsbl_google_plus_one">
    <g:plusone size="medium" annotation="none" href="http://b.l0g.jp/uncategorized/clear-on-cygwin-bash/" ></g:plusone>
  </div>
</div>

<br class='wp_social_bookmarking_light_clear' />

<a href="http://ameblo.jp/principia-ca/entry-11147827610.html" target="_blank">masudakさんのエントリ</a> を読んで、そういえば作業用PCにCygwin入れてないなと思ってインストールしてみたら、clearコマンドがなかった。

学生時代からの癖で事あるごとに「clear;ls」と入力するのが好きな自分は、clearコマンドがないと生きていけない。

どうやらclearコマンドが欲しい場合は、ncursesを入れると使えるようになるようだ。Cygwinのsetup.exeで、「Utils → ncurses」をインストールするようにチェックを入れれば、めでたくclearできるようになる。

* * *

**海外の役立つブログ記事などを人力で翻訳して公開する[Yakst](https://yakst.com/ja)というプロジェクトをやっています。よろしければそちらもどうぞ！**