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
<div class='wp_social_bookmarking_light'>
  <div class="wsbl_hatena_button">
    <a href="http://b.hatena.ne.jp/entry/http://b.l0g.jp/dev/mounting-directory-on-directory/" class="hatena-bookmark-button" data-hatena-bookmark-title="ディレクトリをディレクトリにマウント" data-hatena-bookmark-layout="standard" title="このエントリーをはてなブックマークに追加"> <img src="//b.hatena.ne.jp/images/entry-button/button-only@2x.png" alt="このエントリーをはてなブックマークに追加" width="20" height="20" style="border: none;" /></a>
  </div>
  
  <div class="wsbl_facebook_like">
    <div id="fb-root">
    </div><fb:like href="http://b.l0g.jp/dev/mounting-directory-on-directory/" layout="button_count" action="like" width="100" share="false" show_faces="false" ></fb:like>
  </div>
  
  <div class="wsbl_twitter">
    <a href="https://twitter.com/share" class="twitter-share-button"{count} data-url="http://b.l0g.jp/dev/mounting-directory-on-directory/" data-text="ディレクトリをディレクトリにマウント" data-via="dblmkt " data-lang="ja">Tweet</a>
  </div>
  
  <div class="wsbl_google_plus_one">
    <g:plusone size="medium" annotation="none" href="http://b.l0g.jp/dev/mounting-directory-on-directory/" ></g:plusone>
  </div>
</div>

<br class='wp_social_bookmarking_light_clear' />

Linuxで、ディスクデバイスではなく、ディレクトリをディレクトリにマウントしたい時は

[text]
  
mount &#8211;bind マウントしたいディレクトリ マウントポイント
  
[/text]

fstabに書いてしまうときは以下のように書く。

[text]
  
マウントしたいディレクトリ マウントポイント none bind 0 0
  
[/text]

例えば /home を /hoge/home としてマウントしたい時は

[text]
  
/home /hoge/home none bind 0 0
  
[/text]

なお、マウントしたいディレクトリ(上の例では/hoge)よりも後にこの行を書かないと、マウントできないので注意。

* * *

**海外の役立つブログ記事などを人力で翻訳して公開する[Yakst](https://yakst.com/ja)というプロジェクトをやっています。よろしければそちらもどうぞ！**