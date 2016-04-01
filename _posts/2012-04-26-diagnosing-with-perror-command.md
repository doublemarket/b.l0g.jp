---
id: 1038
title: MySQLエラーログに出てきたエラーコードのとっかかり
date: 2012-04-26T12:02:56+00:00
author: doublemarket
layout: post
guid: http://b.l0g.jp/?p=1038
permalink: /mysql/diagnosing-with-perror-command/
categories:
  - MySQL
---

MySQLのエラーログに記録されるエラーコードは、MySQL自体が判断して表示するものと、OSが返したエラーコードをMySQLが表示しているものとの2種類に分けられる。それぞれまずどう調べ始めるか、という話。<a href="http://www.mysqlperformanceblog.com/2012/03/26/how-to-diagnose-errors-in-the-mysql-error-log/" title="How to diagnose errors in the MySQL error log" target="_blank">MySQL Performance Blogのエントリ</a>に、後で自分の役に立つように情報を加えてみた。

例えば、レプリケーションがうまくいかない時に表示される

[text]
  
Got fatal error 1236 from master when reading data from binary log: &#8216;Client requested master to start replication from impossible position&#8217;
  
[/text]

というエラーの「1236」は、MySQL自体が判断した結果なので、以下のサイトで一覧が見られる。というか、表示されているままで、それ以上の情報はエラーログの前後や他のログ(OSログなど)を確認するしかない。

4.1
  
<a href="http://dev.mysql.com/doc/refman/4.1/ja/error-returns.html" title="MySQL 4.1 リファレンスマニュアル 12.1. 返されるエラー" target="_blank">MySQL 4.1 リファレンスマニュアル 12.1. 返されるエラー</a>

5.0/5.1/5.5 (バージョン部分を変えるだけで全部URLは同じ)
  
<a href="http://dev.mysql.com/doc/refman/5.0/en/error-messages-server.html" target="_blank">MySQL 5.0 Reference Manual C.3. Server Error Codes and Messages</a>
  
<a href="http://dev.mysql.com/doc/refman/5.0/en/error-messages-client.html" target="_blank">MySQL 5.0 Reference Manual C.4. Client Error Codes and Messages</a>

一方、以下のようなエラーの場合、

[text]
  
120326 16:56:45 [ERROR] /usr/sbin/mysqld: Incorrect key file for table &#8216;/tmp/#sql\_21b2\_0.MYI&#8217;; try to repair it
  
120326 16:56:45 [ERROR] Got an error from unknown thread, storage/myisam/mi_write.c:223
  
120326 16:56:45 [ERROR] /usr/sbin/mysqld: Sort aborted: Error writing file &#8216;/tmp/MYK74Kpi&#8217; (Errcode: 28)
  
[/text]

エラーコードを調べないままだと、ファイルへの書き出しで失敗したことまでは分かっても、その先何が起きているのかは分からない。ここで、「Errcode: 28」は、OSが返しているエラーを表示している。これがどのような意味かは、perrorコマンドで分かる。

[bash]
  
$ perror 28
  
OS error code 28: No space left on device
  
[/bash]

エラーログだけでは判断がつかないが、エラーコード28の意味が分かると、単純な問題であることが判明する。MyISAMはディスクがいっぱいであるというエラーを正しく扱わないという問題があるので、このようなことになる。

perrorコマンド、MySQLに付属している小ネタ的コマンドだが、OSエラーコードをそのまま表示する他のソフトウェアのエラーログを見る時にも役立ちそうだ。



* * *

**海外の役立つブログ記事などを人力で翻訳して公開する[Yakst](https://yakst.com/ja)というプロジェクトをやっています。よろしければそちらもどうぞ！**
