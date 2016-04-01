---
id: 690
title: shのwhileループでファイルを読み、中でsshを実行すると1回しかループしない
date: 2011-03-18T14:39:57+00:00
author: doublemarket
layout: post
guid: http://b.l0g.jp/?p=690
permalink: /dev/ssh-inside-while/
categories:
  - 開発
---
<div class='wp_social_bookmarking_light'>
  <div class="wsbl_hatena_button">
    <a href="http://b.hatena.ne.jp/entry/http://b.l0g.jp/dev/ssh-inside-while/" class="hatena-bookmark-button" data-hatena-bookmark-title="shのwhileループでファイルを読み、中でsshを実行すると1回しかループしない" data-hatena-bookmark-layout="standard" title="このエントリーをはてなブックマークに追加"> <img src="//b.hatena.ne.jp/images/entry-button/button-only@2x.png" alt="このエントリーをはてなブックマークに追加" width="20" height="20" style="border: none;" /></a>
  </div>
  
  <div class="wsbl_facebook_like">
    <div id="fb-root">
    </div><fb:like href="http://b.l0g.jp/dev/ssh-inside-while/" layout="button_count" action="like" width="100" share="false" show_faces="false" ></fb:like>
  </div>
  
  <div class="wsbl_twitter">
    <a href="https://twitter.com/share" class="twitter-share-button"{count} data-url="http://b.l0g.jp/dev/ssh-inside-while/" data-text="shのwhileループでファイルを読み、中でsshを実行すると1回しかループしない" data-via="dblmkt " data-lang="ja">Tweet</a>
  </div>
  
  <div class="wsbl_google_plus_one">
    <g:plusone size="medium" annotation="none" href="http://b.l0g.jp/dev/ssh-inside-while/" ></g:plusone>
  </div>
</div>

<br class='wp_social_bookmarking_light_clear' />

シェル(sh)スクリプトネタ。

while 文で以下のように指定すると、行ごとにファイルを読み込むことができる。

[bash]
  
#!/bin/sh

\# ファイルを1行ずつ読み込んで表示

TESTFILE=./hoge.txt

while read line
  
do
      
echo $line
  
done < $TESTFILE
  
[/bash]

しかし、 while ループの中で ssh や rsh を実行すると、読み込むファイルが複数行あっても、1行目しか処理されない。

[bash]
  
#!/bin/sh

\# TESTFILEが複数行あっても1行目のみ処理されてループ終了

while read line
  
do
      
ssh 192.168.0.1 touch $line
  
done < $TESTFILE
  
[/bash]

SSH を実行すると、標準入力がそちらに振り向けられるため、read で読んだ1行のみならず、ファイル全体が SSH に渡されてしまう。従って、SSH を実行した後はもう読める行がないので while ループは1回で終了してしまう。

これを防ぐには、ssh に -n オプションを付け、/dev/null をリダイレクトし、標準入力をリダイレクトしないようにする。

[bash]
  
#!/bin/sh

\# TESTFILEが複数行あればその行数分処理が実行される

while read line
  
do
      
ssh -n 192.168.0.1 touch $line
  
done < $TESTFILE
  
[/bash]

なおこれは rsh コマンドでも同じ仕様になっているようだ。

なぜ1行目だけで終わってしまうのか分からずうんうんうなってたら、この本に書いてあった。



* * *

**海外の役立つブログ記事などを人力で翻訳して公開する[Yakst](https://yakst.com/ja)というプロジェクトをやっています。よろしければそちらもどうぞ！**