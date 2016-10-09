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

シェル(sh)スクリプトネタ。

while 文で以下のように指定すると、行ごとにファイルを読み込むことができる。

```
  
#!/bin/sh

\# ファイルを1行ずつ読み込んで表示

TESTFILE=./hoge.txt

while read line
  
do
      
echo $line
  
done < $TESTFILE
  
```

しかし、 while ループの中で ssh や rsh を実行すると、読み込むファイルが複数行あっても、1行目しか処理されない。

```
  
#!/bin/sh

\# TESTFILEが複数行あっても1行目のみ処理されてループ終了

while read line
  
do
      
ssh 192.168.0.1 touch $line
  
done < $TESTFILE
  
```

SSH を実行すると、標準入力がそちらに振り向けられるため、read で読んだ1行のみならず、ファイル全体が SSH に渡されてしまう。従って、SSH を実行した後はもう読める行がないので while ループは1回で終了してしまう。

これを防ぐには、ssh に -n オプションを付け、/dev/null をリダイレクトし、標準入力をリダイレクトしないようにする。

```
  
#!/bin/sh

\# TESTFILEが複数行あればその行数分処理が実行される

while read line
  
do
      
ssh -n 192.168.0.1 touch $line
  
done < $TESTFILE
  
```

なおこれは rsh コマンドでも同じ仕様になっているようだ。

なぜ1行目だけで終わってしまうのか分からずうんうんうなってたら、この本に書いてあった。



