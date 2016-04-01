---
id: 981
title: MySQLスレーブのmy.cnfにはreport-hostを必ず書こう
date: 2012-04-12T21:06:24+00:00
author: doublemarket
layout: post
guid: http://b.l0g.jp/?p=981
permalink: /mysql/report-host-in-slave-mycnf/
categories:
  - MySQL
---

世の中では当たり前のことなのかもしれないが、なぜか今勤めてる会社では一般的ではないのでメモしておく。

MySQLでレプリケーション構成を取っている時、スレーブとなるマシンのmy.cnfには、server-idと一緒にreport-hostを必ず書いておこう。

```
  
server-id = 一意のID
  
report-host = ホスト名
  
```

(2012.10.15追記) report-hostではなくslave-hostになってたと@ishikawa84gさんから指摘を受けたので修正。

これで、マスターでshow slave hostsコマンドを打つだけで、スレーブの一覧が表示される。

```
  
> show slave hosts
  
+----+------+--+----+
  
| Server\_id | Host | Port | Master\_id |
  
+----+------+--+----+
  
| 16800111 | nanikano-dbs01 | 3306 | 16800101 |
  
| 16800112 | nanikano-dbs02 | 3306 | 16800101 |
  
| 16800113 | nanikano-dbs03 | 3306 | 16800101 |
  
+----+------+--+----+
  
```

report-hostを書いていない時にマスタからスレーブの一覧を取得しようと思うと、マスターに接続しているプロセスの一覧から、スレーブへログを渡しているものを抜き出して知るしかない。以下のように、show processlistの結果から、ユーザがレプリケーション用のもの(change masterした時に指定したユーザ)で、CommandがBinlog Dumpとなっているものがスレーブとのレプリケーション用のプロセスだと分かる。

```
  
> show processlist;

+---+----+-------+----+-----+---+----------------------+------+
  
| Id | User | Host | db | Command | Time | State | Info |
  
+---+----+-------+----+-----+---+----------------------+------+
  
| 1592850 | user00001 | 192.168.0.203:38102 | nantoka | Sleep | 1 | | NULL |
  
| 1592854 | user00001 | 192.168.0.203:38106 | hogefuga | Sleep | 1 | | NULL |
  
| 1592856 | user00001 | 192.168.0.203:38108 | hogefuga | Sleep | 1 | | NULL |
  
| 1596335 | user00001 | 192.168.0.203:38328 | hogefuga | Sleep | 27 | | NULL |
  
| 1596337 | user00001 | 192.168.0.203:38331 | nantoka | Sleep | 15 | | NULL |
    
(中略)
  
| 1596701 | repluser | 192.168.0.72:40783 | NULL | Binlog Dump | 80289 | Has sent all binlog to slave; waiting for binlog to be updated | NULL |
  
| 1596702 | repluser | 192.168.0.73:49388 | NULL | Binlog Dump | 80289 | Has sent all binlog to slave; waiting for binlog to be updated | NULL |
  
| 1596706 | repluser | 192.168.0.55:47014 | NULL | Binlog Dump | 80289 | Has sent all binlog to slave; waiting for binlog to be updated | NULL |
  
| 1596707 | repluser | 192.168.1.81:54804 | NULL | Binlog Dump | 80289 | Has sent all binlog to slave; waiting for binlog to be updated | NULL |
  
+---+----+-------+----+-----+---+----------------------+------+
  
```

多数のアプリケーションサーバからの接続があったりしてコネクション数が大量で、show processlistが多くの行を返す場合などにはこれでは見づらく、show slave hostコマンドできれいに見られるのがうれしい。細かいことだけど、あとで見やすいようにしておくのは結構重要だと思う。

ちなみに、report-hostは「set report-host = hoge」などとオンラインで変更することはできないので、レプリケーションのセットアップ時などセットアップの初期段階で設定しておくべし。

**2012年4月18日追記**

MySQL 5.5.3よりも新しいバージョンの場合、スレーブでreport-hostを設定しなくてもマスタでshow slave hostsを実行すると一覧が表示されるようになっているようだ(<a href="http://dev.mysql.com/doc/refman/5.5/en/show-slave-hosts.html" title="MySQL 5.5 Reference Manual 13.7.5.34. SHOW SLAVE HOSTS Syntax" target="_blank">出典</a>)。5.5.3以前の場合、report-hostを書かないとそのスレーブは表示されてこない。

```
  
mysql> show slave hosts;
  
+----+--+--+----+
  
| Server\_id | Host | Port | Master\_id |
  
+----+--+--+----+
  
| 111 | | 3306 | 101 |
  
| 112 | | 3306 | 101 |
  
+----+--+--+----+
  
2 rows in set (0.00 sec)
  
```

ただしこの場合Hostの欄は上のように空になり、report-hostを設定するとその値がHost列に表示されるようになっている。

* * *

**海外の役立つブログ記事などを人力で翻訳して公開する[Yakst](https://yakst.com/ja)というプロジェクトをやっています。よろしければそちらもどうぞ！**
