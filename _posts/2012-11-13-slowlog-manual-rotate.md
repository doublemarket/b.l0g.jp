---
id: 1361
title: MySQLスローログの手動ローテート
date: 2012-11-13T13:18:15+00:00
author: doublemarket
layout: post
guid: http://b.l0g.jp/?p=1361
permalink: /mysql/slowlog-manual-rotate/
categories:
  - MySQL
---

<a href="http://b.l0g.jp/mysql/slowlog-manual-rotate/attachment/mysql/" rel="attachment wp-att-1371"><img class="alignnone size-full wp-image-1371" style="border: 0px;" title="mysql" src="http://b.l0g.jp/wp-content/uploads/2012/11/mysql.png" alt="" width="170" height="115" /></a>

MySQLのスローログは、通常それほど大量に出るものではないからか、自動でローテートされるような設定は特にない。しかし、負荷が上がったりしてスローログが大量に出てしまい、ローテートしたい場合はよくあるだろう。手動でローテートする場合は以下のように行う。

この例は、MySQLのデータディレクトリ(datadir)が /var/lib/mysql で、スローログファイル名(slow\_query\_log_file)がmysql-slow.logの場合。

[sql]
  
$ cd /var/lib/mysql
  
$ mv mysql-slow.log mysql-slow.log.old
  
$ mysqladmin flush-logs
  
または
  
mysql> flush logs;
  
[/sql]

mysqladmin flush-logs あるいは flush logs で、ログファイルを開き直すことができる。

なお、5.1.12以降だとログ出力をコンソールからオフにできるので、オフにしてからflush-logsし、その後オンに戻すとよいだろう。

[sql]
  
mysql> set global slow\_query\_log = &#8216;OFF&#8217;;
  
mysql> set global slow\_query\_log = &#8216;ON&#8217;;
  
[/sql]

ここで、MySQL5.1以前のバージョンでは「mysqladmin flush-logs」を実行すると、バイナリログや一般クエリログも全部まとめてローテートされてしまう。binlogを有効にしているサーバ(レプリケーションしている構成のマスタなど)では注意した方がいいかもしれない。

なお、5.5からは、

[sql]

# スローログだけ開き直す

mysql> flush slow logs;

# 一般クエリログだけ開き直す

mysql> flush general logs;

# バイナリログだけローテート

mysql> flush binary logs;

# エラーログだけローテート

mysql> flush error logs;
  
[/sql]

というように、種類を指定できるようになっている。
  
(2014/12/17 エラーログについてコメントで指摘いただいたので追加。情報提供ありがとうございます)

ちなみにlogrotateで定期的にローテートする場合はこうやるそうだ。
  
[MySQL の slowlog を logrotate する方法 | Carpe Diem](http://www.sssg.org/blogs/naoya/archives/1251)
  
定期的にローテートする必要があるほどスローログが出るままにするってのはどうかと思うが、そういうこともあるかもｗ

* * *

**海外の役立つブログ記事などを人力で翻訳して公開する[Yakst](https://yakst.com/ja)というプロジェクトをやっています。よろしければそちらもどうぞ！**
