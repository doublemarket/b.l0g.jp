---
id: 655
title: sarコマンドでシステムのボトルネックを探る(1)
date: 2011-02-17T21:59:39+00:00
author: doublemarket
layout: post
guid: http://b.l0g.jp/?p=655
permalink: /dev/finding-bottleneck-with-sar1/
categories:
  - 開発
---

<a href="http://b.l0g.jp/dev/top-loadaverag/" target="_blank">前回のエントリ</a>では、システムのレスポンスが悪化している場合、まずとっかかりとしてtopコマンドを実行し、ロードアベレージを確認する方法を書いた。さらに踏み込んで、何が負荷の原因なのかを探るために、sarコマンドを使う方法について書く。

sarコマンドをオプションをつけずに実行すると、以下のような出力が得られる。ファイルを指定しないと、コマンドを実行した当日の0:00から直前までの10分おきの情報が表示される。過去の情報は/var/log/saディレクトリ以下に保存されており、「sar -f sa07」などと指定すると、そのファイルに保存された情報が閲覧できる。saの後の数字は日付を表している。

sarを実行してもコマンドが見つからない場合、sysstatパッケージがインストールされていないので、インストールすべし。

[text]
  
[doublemarket@hoge ~]$ sar
  
Linux 2.6.18-194.8.1.el5 (hoge) 2011年02月15日

00時00分01秒 CPU %user %nice %system %iowait %steal %idle
  
00時10分01秒 all 0.05 0.00 0.08 0.02 0.00 99.85
  
00時20分01秒 all 0.03 0.00 0.08 0.02 0.00 99.88
  
00時30分01秒 all 0.07 0.00 0.11 0.02 0.00 99.79
  
:
  
:
  
20時00分01秒 all 0.03 0.00 0.06 0.00 0.00 99.90
  
20時10分01秒 all 0.06 0.00 0.11 0.03 0.00 99.80
  
20時20分01秒 all 0.16 0.00 0.14 0.03 0.00 99.67
  
20時30分01秒 all 1.11 0.00 0.27 0.12 0.00 98.50
  
20時40分01秒 all 0.08 0.00 0.12 0.05 0.00 99.76
  
平均値: all 0.08 0.00 0.10 0.09 0.00 99.73
  
[/text]

ここで得られる結果は、sar -uと同じものである。それぞれの列の意味は以下の通り。

<dt style="padding-left: 30px;">
  <strong>user</strong>
</dt>

<dd style="padding-left: 60px;">
  ユーザプログラムの実行に使用されたCPUリソースの割合
</dd>

<dt style="padding-left: 30px;">
  <strong>nice</strong>
</dt>

<dd style="padding-left: 60px;">
  niceによる優先度を処理するために使用されたCPUリソースの割合
</dd>

<dt style="padding-left: 30px;">
  <strong>system</strong>
</dt>

<dd style="padding-left: 60px;">
  カーネルが使用したCPUリソースの割合
</dd>

<dt style="padding-left: 30px;">
  <strong>iowait</strong>
</dt>

<dd style="padding-left: 60px;">
  I/O待ちの割合
</dd>

<dt style="padding-left: 30px;">
  <strong>idle</strong>
</dt>

<dd style="padding-left: 60px;">
  I/O待ち以外でCPUが待ち状態だった割合(何もしていない時間)
</dd>

なお、マルチCPUの環境の場合ここに表示されているのは、全CPUの合算使用率になっている。それぞれのCPUの使用率を確認したい場合、「sar -P ALL」を実行する。意外と処理が特定のCPUに偏っていたりするので、こちらも確認してみた方がよい。

→ sarコマンドの実行結果からシステムの負荷状況に関して、以下の傾向がつかめる。

<dt style="padding-left: 30px;">
  <strong>userの値だけが高い(他の値は低いまま)</strong>
</dt>

<dd style="padding-left: 60px;">
  CPUリソースを必要とするアプリケーションが動作している。<br /> topやps auwxなどのコマンドでプロセスごとのCPU使用率を調べ、無限ループなどの不具合によるCPU使用率の上昇でないかなど、異常を確認。単に処理が重くなっているだけなら、スケールアップ(CPUを高速化)、スケールアウト(マシンを増設)を検討する。
</dd>

<dt style="padding-left: 30px;">
  <strong>iowaitの値が高い</strong>
</dt>

<dd style="padding-left: 60px;">
  そのマシンに求められる処理に対して、ハードディスクの速度が追いついていない。アプリケーションやミドルウェア(特にデータベースなど)のキャッシュの仕組みを見直したり、読み書きに使う物理的なディスクを分散させるなどして、なるべくディスクの読み書きが発生しない方法を考える。それでも高いままなら、より高速なディスクを使うしかない。なおたいていの場合は<br /> ・アプリケーション自体がディスクにアクセスする速度が遅い<br /> ・メモリが不足していてスワップ領域(つまりディスク)へのアクセスが頻発していて遅い<br /> の2つに問題を分けることができる。このうちのどちらかという判断は、sar -rの結果などを見る必要がある。
</dd>

<dt style="padding-left: 30px;">
  <strong>systemの値が高い</strong>
</dt>

<dd style="padding-left: 60px;">
  仕組み的にいえば、コンテキストスイッチがたくさん発生していると値が上がるはずなので、必要以上にたくさんのプログラムを並列に処理しすぎていると、この値が異常に上がるということになるが、よく分からない。
</dd>

sarコマンドには、メモリやディスク、ネットワークなどに関する情報が得られるよう、他にも色々なオプションがある。

* * *

**海外の役立つブログ記事などを人力で翻訳して公開する[Yakst](https://yakst.com/ja)というプロジェクトをやっています。よろしければそちらもどうぞ！**
