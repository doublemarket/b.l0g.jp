---
id: 673
title: sarコマンドでシステムのボトルネックを探る(2)
date: 2011-02-21T22:07:48+00:00
author: doublemarket
layout: post
guid: http://b.l0g.jp/?p=673
permalink: /dev/finding-bottleneck-with-sar2/
categories:
  - 開発
---
<div class='wp_social_bookmarking_light'>
  <div class="wsbl_hatena_button">
    <a href="http://b.hatena.ne.jp/entry/http://b.l0g.jp/dev/finding-bottleneck-with-sar2/" class="hatena-bookmark-button" data-hatena-bookmark-title="sarコマンドでシステムのボトルネックを探る(2)" data-hatena-bookmark-layout="standard" title="このエントリーをはてなブックマークに追加"> <img src="//b.hatena.ne.jp/images/entry-button/button-only@2x.png" alt="このエントリーをはてなブックマークに追加" width="20" height="20" style="border: none;" /></a>
  </div>
  
  <div class="wsbl_facebook_like">
    <div id="fb-root">
    </div><fb:like href="http://b.l0g.jp/dev/finding-bottleneck-with-sar2/" layout="button_count" action="like" width="100" share="false" show_faces="false" ></fb:like>
  </div>
  
  <div class="wsbl_twitter">
    <a href="https://twitter.com/share" class="twitter-share-button"{count} data-url="http://b.l0g.jp/dev/finding-bottleneck-with-sar2/" data-text="sarコマンドでシステムのボトルネックを探る(2)" data-via="dblmkt " data-lang="ja">Tweet</a>
  </div>
  
  <div class="wsbl_google_plus_one">
    <g:plusone size="medium" annotation="none" href="http://b.l0g.jp/dev/finding-bottleneck-with-sar2/" ></g:plusone>
  </div>
</div>

<br class='wp_social_bookmarking_light_clear' />

CPUの処理の状況を調べるためには、sar (sar -u)が有効であることを<a href="http://b.l0g.jp/dev/finding-bottleneck-with-sar1/" target="_blank">前のエントリ</a>で書いた。次はメモリの使用状況を調べてみる。まず見てみるのは、メモリとスワップの使用状況を示す sar -r の結果である。

[text]
  
[doublemarket@hoge ~]$ sar -r
  
Linux 2.6.18-194.8.1.el5 (hoge) 2011年02月15日

00時00分01秒 kbmemfree kbmemused %memused kbbuffers kbcached kbswpfree kbswpused %swpused kbswpcad
  
00時10分01秒 12964 497568 97.46 30880 107440 1984336 63940 3.12 16392
  
00時20分01秒 13064 497468 97.44 30972 107376 1984336 63940 3.12 16392
  
00時30分01秒 12692 497840 97.51 31092 107236 1984336 63940 3.12 16392
  
:
  
:
  
20時20分01秒 43756 466776 91.43 45624 139552 1984336 63940 3.12 16288
  
20時30分01秒 27008 483524 94.71 46420 147608 1984336 63940 3.12 16288
  
20時40分01秒 22664 487868 95.56 46840 148920 1984336 63940 3.12 16288
  
20時50分01秒 22300 488232 95.63 47192 148968 1984336 63940 3.12 16288
  
平均値: 84267 426265 83.49 32592 129944 1984336 63940 3.12 16300
  
[/text]

それぞれの列の意味は以下の通り。

<dt style="padding-left: 30px;">
  <strong>kbmemfree</strong>
</dt>

<dd style="padding-left: 60px;">
  物理メモリの未使用KB。
</dd>

<dt style="padding-left: 30px;">
  <strong>kbmemused</strong>
</dt>

<dd style="padding-left: 60px;">
  物理メモリの使用済みKB。
</dd>

<dt style="padding-left: 30px;">
  <strong>%memused</strong>
</dt>

<dd style="padding-left: 60px;">
  物理メモリの使用率。Linuxでは、使用されていないメモリ領域をファイルシステムキャッシュ領域として使用するので、通常はほとんどfree部分はないものと考えてよい。従って、常に90数%といった高い使用率になる。実際にアプリケーションの動作に使われているメモリの量は、下の指標を確認する必要がある。
</dd>

<dt style="padding-left: 30px;">
  <strong>kbbuffers</strong>
</dt>

<dd style="padding-left: 60px;">
  カーネルがバッファ領域として使用しているメモリ量のKB。
</dd>

<dt style="padding-left: 30px;">
  <strong>kbcached</strong>
</dt>

<dd style="padding-left: 60px;">
  カーネルがキャッシュとして使用しているメモリ量のKB。kbmemusedからkbbufferesとkbcachedを引いた値が、実際にアプリケーションに割り当てられているメモリ量ということになる。逆に言えば、kbbufferesとkbcachedの容量が、kbmemusedの大部分を占めているようなら、アプリケーションにとってメモリ不足とは言えないということになる。
</dd>

<dt style="padding-left: 30px;">
  <strong>kbswpfree</strong>
</dt>

<dd style="padding-left: 60px;">
  スワップ領域の未使用量KB。
</dd>

<dt style="padding-left: 30px;">
  <strong>kbswpused</strong>
</dt>

<dd style="padding-left: 60px;">
  スワップ領域の使用済みKB。
</dd>

<dt style="padding-left: 30px;">
  <strong>%swpused</strong>
</dt>

<dd style="padding-left: 60px;">
  スワップ領域の使用量。通常ほとんど0だが、物理メモリが不足してくると使用率が上がってくる。数%であれば実質的にシステムの動作には影響がない場合が多いが、あまり多いようだと、スワップ領域へのアクセスが頻発している可能性もあり、システム全体のスループットを落とすことがある。%swpusedが大きく、かつsar -uのiowaitの値も大きい場合、ほぼ間違いなくスワップ領域へのアクセスでスローダウンしているので、物理メモリの増設などのメモリ不足解消策を取る必要がある。
</dd>

<dt style="padding-left: 30px;">
  <strong>kbswpcad</strong>
</dt>

<dd style="padding-left: 60px;">
  何の指標かよく分からなかったが、manページによると、一旦スワップ領域へ飛ばされたものの(スワップアウト)、スワップ領域からメモリに戻され(スワップイン)、かつまだスワップ領域にデータが残っている、というデータの量とのこと。
</dd>

各列の値の傾向を見ると、上記のようにメモリ不足なのかどうかの手がかりが得られる。その他に詳しい情報を見たいときは、以下のオプションも用意されている。

<dt style="padding-left: 30px;">
  <strong>sar -R</strong>
</dt>

<dd style="padding-left: 60px;">
  メモリ上のページの使用状況。
</dd>

<dt style="padding-left: 30px;">
  <strong>sar -W</strong>
</dt>

<dd style="padding-left: 60px;">
  スワップの発生状況。ページイン(スワップ領域から物理メモリへのページの移動)とページアウト(物理メモリからスワップ領域へのページの移動)がどの程度発生したのかを確認できる。
</dd>

<dt style="padding-left: 30px;">
  <strong>sar -B</strong>
</dt>

<dd style="padding-left: 60px;">
  ページングの状況。ページイン・ページアウトされたデータ容量、ページフォルトの回数、メジャーなページフォルトの回数をそれぞれ確認できる。
</dd>

* * *

**海外の役立つブログ記事などを人力で翻訳して公開する[Yakst](https://yakst.com/ja)というプロジェクトをやっています。よろしければそちらもどうぞ！**