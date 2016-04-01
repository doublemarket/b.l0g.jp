---
id: 998
title: TCP/IPの送信用ポート範囲を変更する
date: 2012-04-17T20:00:53+00:00
author: doublemarket
layout: post
guid: http://b.l0g.jp/?p=998
permalink: /linux/ip-local-port-range/
categories:
  - Linux
---
<div class='wp_social_bookmarking_light'>
  <div class="wsbl_hatena_button">
    <a href="http://b.hatena.ne.jp/entry/http://b.l0g.jp/linux/ip-local-port-range/" class="hatena-bookmark-button" data-hatena-bookmark-title="TCP/IPの送信用ポート範囲を変更する" data-hatena-bookmark-layout="standard" title="このエントリーをはてなブックマークに追加"> <img src="//b.hatena.ne.jp/images/entry-button/button-only@2x.png" alt="このエントリーをはてなブックマークに追加" width="20" height="20" style="border: none;" /></a>
  </div>
  
  <div class="wsbl_facebook_like">
    <div id="fb-root">
    </div><fb:like href="http://b.l0g.jp/linux/ip-local-port-range/" layout="button_count" action="like" width="100" share="false" show_faces="false" ></fb:like>
  </div>
  
  <div class="wsbl_twitter">
    <a href="https://twitter.com/share" class="twitter-share-button"{count} data-url="http://b.l0g.jp/linux/ip-local-port-range/" data-text="TCP/IPの送信用ポート範囲を変更する" data-via="dblmkt " data-lang="ja">Tweet</a>
  </div>
  
  <div class="wsbl_google_plus_one">
    <g:plusone size="medium" annotation="none" href="http://b.l0g.jp/linux/ip-local-port-range/" ></g:plusone>
  </div>
</div>

<br class='wp_social_bookmarking_light_clear' />

Linuxで、TCP/IPの送信時に使用するポートの範囲を決めるのは、net.ipv4.ip\_local\_port_range というカーネルパラメータ。手元のCentOSでは以下がデフォルト値になっていた。

[bash]
  
\# sysctl net.ipv4.ip\_local\_port_range
  
net.ipv4.ip\_local\_port_range = 32768 61000
  
[/bash]

これだと、32768 から 61000 までの 28232 個の中からランダムに選ばれたポートを使って、サーバ側にアクセスする。このポートが足りなくなった場合、/etc/sysctl.conf に設定を書き込んでOSを再起動することで、範囲を変えられる。

[bash]
  
\# /etc/sysctl.conf に以下を追記
  
net.ipv4.ip\_local\_port_range = 1024 65000
  
[/bash]

再起動

[bash]
  
\# sysctl net.ipv4.ip\_local\_port_range
  
net.ipv4.ip\_local\_port_range = 1024 65000
  
[/bash]

これで1024から65000までのポートを使用できるようになる。

* * *

**海外の役立つブログ記事などを人力で翻訳して公開する[Yakst](https://yakst.com/ja)というプロジェクトをやっています。よろしければそちらもどうぞ！**