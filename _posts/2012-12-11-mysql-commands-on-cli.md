---
id: 1374
title: MySQLコマンドラインツールで使えるコマンド小ネタ
date: 2012-12-11T15:29:16+00:00
author: doublemarket
layout: post
guid: http://b.l0g.jp/?p=1374
permalink: /mysql/mysql-commands-on-cli/
categories:
  - MySQL
---

知らなくても何とかなるけど、知っておくと役に立つmysqlシェルから使えるコマンド。

### systemコマンド

MySQLのプロンプトを開いたままで、ロードアベレージを見たいとか、SQLを書いておいたファイル名を見たい、という時は、OSのシェルで実行できるコマンドをMySQLシェルから実行するsystemコマンドを使えばよい。

```
  
mysql> system uptime
  
10:47:48 up 24 min, 5 users, load average: 3.94, 3.68, 2.57

mysql> system ls
  
test.sh sample01.sql
  
```

### teeコマンド

実行結果をターミナルにも表示するがファイルにも保存しておきたい時はteeコマンド。ファイルへの書き出しをやめる時はnotee。ファイルには、プロンプトから入力した文字列なども全部追記されていく。

```
  
mysql> tee output.txt
  
mysql> show tables;
  
+-------------+
  
| Tables\_in\_test |
  
+-------------+
  
| TABLE01 |
  
| TABLE02 |
  
| TABLE03 |
  
+-------------+
  
3 rows in set (0.00 sec)
  
mysql> notee
  
Outfile disabled.
  
mysql> quit

$ cat output.txt
  
mysql> show tables;
  
+-------------+
  
| Tables\_in\_test |
  
+-------------+
  
| TABLE01 |
  
| TABLE02 |
  
| TABLE03 |
  
+-------------+
  
3 rows in set (0.00 sec)
  
3 rows in set (0.00 sec)
  
mysql> notee
  
$
  
```

### pagerコマンド

```
  
mysql> pager less;
  
```

で、ページャをlessにできるので、実行結果が長い時などに重宝する。これは結構有名な気がするが、それだけではなく、grepなどのコマンドをセットすることもできるので、いろんなことができる。ちなみにlessの -S オプションをつけると、カラムが多くて横に広がってしまう結果も横スクロールできるようになる。

```
  
mysql> pager grep Behind
  
PAGER set to 'grep Behind'
  
mysql> show slave status\G
  
Seconds\_Behind\_Master: 3133
  
1 row in set (0.00 sec)
  
```

こんな感じで必要な行だけを取り出すこともできる。また、パイプも使うことができるので、以下のように組み合わせることも可能。あんまりいい例が考え付かなくて微妙ｗ

```
  
mysql> select * from test;
  
+--+--+--+--+--+--+--+--+
  
| a | b | c | d | e | f | g | h |
  
+--+--+--+--+--+--+--+--+
  
| 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 |
  
+--+--+--+--+--+--+--+--+
  
1 row in set (0.01 sec)

mysql> pager grep -v '-' | tr -d '|'
  
PAGER set to 'grep -v '-' | tr -d '|"
  
mysql> select * from test;
  
a b c d e f g h
  
1 2 3 4 5 6 7 8
  
1 row in set (0.00 sec)
  
```

ページャを元に(標準出力に)戻すには

```
  
mysql> nopager;
  
PAGER set to stdout
  
```



