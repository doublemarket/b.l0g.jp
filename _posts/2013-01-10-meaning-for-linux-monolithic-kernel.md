---
id: 1390
title: Linuxがモノリシックカーネルである理由
date: 2013-01-10T08:53:06+00:00
author: doublemarket
layout: post
guid: http://b.l0g.jp/?p=1390
permalink: /linux/meaning-for-linux-monolithic-kernel/
categories:
  - Linux
---
<div class='wp_social_bookmarking_light'>
  <div class="wsbl_hatena_button">
    <a href="http://b.hatena.ne.jp/entry/http://b.l0g.jp/linux/meaning-for-linux-monolithic-kernel/" class="hatena-bookmark-button" data-hatena-bookmark-title="Linuxがモノリシックカーネルである理由" data-hatena-bookmark-layout="standard" title="このエントリーをはてなブックマークに追加"> <img src="//b.hatena.ne.jp/images/entry-button/button-only@2x.png" alt="このエントリーをはてなブックマークに追加" width="20" height="20" style="border: none;" /></a>
  </div>
  
  <div class="wsbl_facebook_like">
    <div id="fb-root">
    </div><fb:like href="http://b.l0g.jp/linux/meaning-for-linux-monolithic-kernel/" layout="button_count" action="like" width="100" share="false" show_faces="false" ></fb:like>
  </div>
  
  <div class="wsbl_twitter">
    <a href="https://twitter.com/share" class="twitter-share-button"{count} data-url="http://b.l0g.jp/linux/meaning-for-linux-monolithic-kernel/" data-text="Linuxがモノリシックカーネルである理由" data-via="dblmkt " data-lang="ja">Tweet</a>
  </div>
  
  <div class="wsbl_google_plus_one">
    <g:plusone size="medium" annotation="none" href="http://b.l0g.jp/linux/meaning-for-linux-monolithic-kernel/" ></g:plusone>
  </div>
</div>

<br class='wp_social_bookmarking_light_clear' />

Quoraの問答は本当に面白いものばかり。技術的でないのもかなり興味深いものが多いが、またLinuxについてのネタで分かりやすいのがあったので訳してみた。

**[Linuxがモノリシックカーネルであるというのはどういう意味か？](http://www.quora.com/Linux/What-is-meant-by-Linux-has-a-monolithic-kernel)**

**モノリシックカーネル**

モノリシックカーネルは、プロセスやメモリの管理、例外の取扱い、入出力のやり取り、ファイルシステムなどほとんどの基本的なシステムサービスを実行する。層構造になっており、基礎的なプロセス管理からOSの他の部分(ライブラリ群や、層の最上位であるアプリケーション)とのインタフェースからなっている。基本的なサービスをカーネルスペースに含めることは、次のような大きな3つの欠点となる。

  * カーネルサイズが大きくなる
  * 拡張性に乏しい
  * メンテナンス性が悪い

**マイクロカーネル**

カーネルの機能を基本的なプロセス間通信と入出力制御のみに絞り、ユーザスペースの他のシステムサービスを普通のプロセス(「サーバ」と呼ばれる)としてしまうのがコンセプトである。あるサーバはメモリに関する管理を行い、別のサーバはプロセス管理、さらに他のサーバはドライバの管理…といった具合だ。サーバはカーネルスペースでは実行されないので、ユーザプロセスが特権モードに入ったり出たりするためにいわゆるコンテキストスイッチが発生してしまう。この方式では、マイクロカーネルはもはやシステムサービスの塊ではなく、プロセス間通信とプロセスとその下のハードウェアとの通信を制御するための、単にいくつかの基本的な抽象化とプリミティブを提供するだけである。通信は直接行われないので、独立した通信と拡張性をもたらす、メッセージングの仕組みが使われる。

ここでLinuxカーネルの話に入ろう。Linuxカーネルは、モノリシックカーネルである。しかし、Linuxの美しいところは、どちらとしてもコンパイルできることである！

モノリシックカーネル

  * 1つのバイナリファイル
  * 全ドライバがカーネル自体に含まれる

モジュラーカーネル

  * 複数のカーネルのファイル
  * ドライバはmodprobeコマンドなどでカーネルにロードしたりアンロード可能。lsmodやmodprobeのmanページを参照
  * ほとんど全てのドライバはカーネルに対してビルドされてリンクされる

以下のカーネルコンフィグオプションの設定でモジュラーカーネルをビルドすることができる。

[text]
  
Enable loadable module support (CONFIG_MODULES) [Y/n/?]
  
[/text]

このオプションにYと答えるとモジュラーカーネルとなり、各機能やドライバそれぞれに関して3つの選択肢が現われる。

<table class="article">
  <tr>
    <td>
      m
    </td>
    
    <td>
      ドライバをモジュールとしてコンパイルできる
    </td>
  </tr>
  
  <tr>
    <td>
      y
    </td>
    
    <td>
      カーネル自身に組み込む
    </td>
  </tr>
  
  <tr>
    <td>
      n
    </td>
    
    <td>
      機能・ドライバを含まない
    </td>
  </tr>
</table>

(CONFIG_MODULES)オプションを n に設定した場合モノリシックカーネルとなり、各機能やドライバそれぞれに2つの選択肢がある。

<table class="article">
  <tr>
    <td>
      y
    </td>
    
    <td>
      機能・ドライバを含む
    </td>
  </tr>
  
  <tr>
    <td>
      n
    </td>
    
    <td>
      機能・ドライバを含まない
    </td>
  </tr>
</table>

&nbsp;



* * *

**海外の役立つブログ記事などを人力で翻訳して公開する[Yakst](https://yakst.com/ja)というプロジェクトをやっています。よろしければそちらもどうぞ！**