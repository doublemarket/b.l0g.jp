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

