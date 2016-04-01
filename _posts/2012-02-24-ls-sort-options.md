---
id: 953
title: lsコマンドのソートオプションまとめ
date: 2012-02-24T21:57:39+00:00
author: doublemarket
layout: post
guid: http://b.l0g.jp/?p=953
permalink: /shell/ls-sort-options/
categories:
  - シェル
---
<div class='wp_social_bookmarking_light'>
  <div class="wsbl_hatena_button">
    <a href="http://b.hatena.ne.jp/entry/http://b.l0g.jp/shell/ls-sort-options/" class="hatena-bookmark-button" data-hatena-bookmark-title="lsコマンドのソートオプションまとめ" data-hatena-bookmark-layout="standard" title="このエントリーをはてなブックマークに追加"> <img src="//b.hatena.ne.jp/images/entry-button/button-only@2x.png" alt="このエントリーをはてなブックマークに追加" width="20" height="20" style="border: none;" /></a>
  </div>
  
  <div class="wsbl_facebook_like">
    <div id="fb-root">
    </div><fb:like href="http://b.l0g.jp/shell/ls-sort-options/" layout="button_count" action="like" width="100" share="false" show_faces="false" ></fb:like>
  </div>
  
  <div class="wsbl_twitter">
    <a href="https://twitter.com/share" class="twitter-share-button"{count} data-url="http://b.l0g.jp/shell/ls-sort-options/" data-text="lsコマンドのソートオプションまとめ" data-via="dblmkt " data-lang="ja">Tweet</a>
  </div>
  
  <div class="wsbl_google_plus_one">
    <g:plusone size="medium" annotation="none" href="http://b.l0g.jp/shell/ls-sort-options/" ></g:plusone>
  </div>
</div>

<br class='wp_social_bookmarking_light_clear' />

lsコマンドにはソートオプションがいくつかあるけど、いつもどれがどれだか分からなくなるのでメモ。

## 時刻でソートするもの

### -c

<p style="padding-left: 30px;">
  -ltと一緒に指定すると、ctime(最終ステータス変更時刻)を表示してそれでソート
</p>

<p style="padding-left: 30px;">
  -lと一緒に指定すると、ctimeを表示するがファイル名でソート
</p>

### -u

<p style="padding-left: 30px;">
  -ltと一緒に指定すると、atime(最終アクセス時刻)を表示してそれでソート
</p>

<p style="padding-left: 30px;">
  -lと一緒に指定すると、atimeを表示するがファイル名でソート
</p>

### -t

<p style="padding-left: 30px;">
  タイムスタンプでソート
</p>

## 名前でソートするもの

### -X

<p style="padding-left: 30px;">
  拡張子のアルファベット順にソート
</p>

### -v

<p style="padding-left: 30px;">
  バージョン順にソート
</p>

## サイズでソートするもの

### -S

<p style="padding-left: 30px;">
  ファイルサイズでソート(大きいものが上)
</p>

## その他

### -U

<p style="padding-left: 30px;">
  ソートせずにディレクトリに置かれている順に表示
</p>

### -f

<p style="padding-left: 30px;">
  ソートしない
</p>

### -r

<p style="padding-left: 30px;">
  指定されたオプションの逆順でソート
</p>

## ソート以外の便利なオプション

### -m

<p style="padding-left: 30px;">
  コンマ区切り
</p>

### -Q

<p style="padding-left: 30px;">
  ダブルクォーテーションでくくられる
</p>

* * *

**海外の役立つブログ記事などを人力で翻訳して公開する[Yakst](https://yakst.com/ja)というプロジェクトをやっています。よろしければそちらもどうぞ！**