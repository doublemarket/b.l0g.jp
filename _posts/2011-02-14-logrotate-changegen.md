---
id: 641
title: logrotateのローテート世代数を減らす時の注意
date: 2011-02-14T22:38:25+00:00
author: doublemarket
layout: post
guid: http://b.l0g.jp/?p=641
permalink: /dev/logrotate-changegen/
categories:
  - 開発
---

本当は「山に行きました！」というブログを投稿したいところなのだが、さっぱり行ける雰囲気がないので、腹いせに技術系の投稿を続けてる。

logrotateを使ってログのローテートを行っている状況で、ログファイルの総容量が大きくなり、世代数を減らさざるを得ないときのこと。「rotate 世代数」の指定を減らしても、古い世代数は削除されない。

具体的には、以下の例。当初logrotate.confで以下の設定をしていたとする。

```
  
\# see "man logrotate" for details
  
\# rotate log files daily
  
daily

\# keep 10 days worth of backlogs
  
rotate 10

\# create new (empty) log files after rotating old ones
  
create

\# uncomment this if you want your log files compressed
  
#compress

\# RPM packages drop log rotation information into this directory
  
include /etc/logrotate.d
  
```

この場合、1日1回ローテートされたファイルが10回分保存される。例えば以下のように。

```
  
ls -v /var/log/httpd/access_log*
  
access\_log access\_log.4 access_log.8
  
access\_log.1 access\_log.5 access_log.9
  
access\_log.2 access\_log.6 access_log.10
  
access\_log.3 access\_log.7
  
```

ここで、アクセスログ1ファイルずつの容量が増えてきて、ディスク空き容量が減ってきた場合、保存する世代数を減らして対処する場合、「rotate 世代数」の世代数を減らしても、古い世代が一斉に削除されるわけではない。

rotate 5とした場合、次のローテート後には以下のようになる。

```
  
access\_log access\_log.4 access_log.9
  
access\_log.1 access\_log.5 access_log.10
  
access\_log.2 access\_log.7
  
access\_log.3 access\_log.8
  
```

つまり、6世代目が消えるだけで、7世代目以降は自動的には削除されないため、手動で削除する必要がある。

これだけ。これが分からなくて危なくディスク使用率が100%になるところだった。

