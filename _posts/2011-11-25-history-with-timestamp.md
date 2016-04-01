---
id: 890
title: historyに時刻を記録する
date: 2011-11-25T00:05:53+00:00
author: doublemarket
layout: post
guid: http://b.l0g.jp/?p=890
permalink: /shell/history-with-timestamp/
categories:
  - シェル
---

bash 3.0以上(最近のLinuxは大抵3.0以上)を使用している場合は、「HISTTIMEFORMAT」というシェル変数に時刻のフォーマットを入れることで、historyコマンドの実行結果(ファイル)にコマンドだけではなく時刻も記録できる。以下はhistoryコマンドの実行結果の例。

[text]
  
100 2011-11-22 10:16:59 ls
  
101 2011-11-22 10:17:03 cd /var/log/
  
102 2011-11-22 10:17:04 ls
  
[/text]

ホームディレクトリ以下の.bashrcや/etc/profile(全ユーザに適用したい場合)に

[text]
  
HISTTIMEFORMAT=&#8217;%Y-%m-%d %T &#8216;
  
[/text]

と書いておけばよい。時刻のフォーマットはdateコマンドと同じものが使えるようだ(man dateを参照)。

なお、historyコマンドでは上記のようなフォーマットが有効だが、.bash_historyファイルには、

[text]
  
#1321924619
  
ls
  
#1321924623
  
cd /var/log/
  
#1321924624
  
ls
  
[/text]

のように、UNIX時刻が付加される形で記録される。

* * *

**海外の役立つブログ記事などを人力で翻訳して公開する[Yakst](https://yakst.com/ja)というプロジェクトをやっています。よろしければそちらもどうぞ！**
