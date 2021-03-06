---
id: 935
title: MySQL ユーザのホストをワイルドカードで指定してもlocalhostは含まれない
date: 2012-02-03T20:07:36+00:00
author: doublemarket
layout: post
guid: http://b.l0g.jp/?p=935
permalink: /mysql/user-at-localhost/
categories:
  - MySQL
---

MySQLをインストールしたので、とりあえずワイルドカードでホスト名を指定してテスト用のユーザを作成。

```

mysql> show databases;
  
+-------+
  
| Database |
  
+-------+
  
| information_schema |
  
| hoge |
  
| mysql |
  
| performance_schema |
  
| test |
  
+-------+
  
5 rows in set (0.00 sec)

mysql> grant select on hoge.* to testuser@'%' identified by 'password';
  
Query OK, 0 rows affected (0.07 sec)

mysql> select host,user from mysql.user;
  
+----+----+
  
| host | user |
  
+----+----+
  
| % | testuser |
  
| 127.0.0.1 | root |
  
| localhost | |
  
| localhost | root |
  
+----+----+
  
7 rows in set (0.00 sec)

mysql> quit

```

この状態で、MySQLがインストールされているサーバ自体からそのままtestuserでログインしようとすると、アクセスが拒否される。

```

testserver$ mysql -utestuser -p
  
Enter password:
  
ERROR 1045 (28000): Access denied for user 'testuser'@'localhost' (using password: YES)

```

しかし、他のサーバからアクセスしてみると、接続は成功して、hogeデータベースへのアクセスもできる。

```

anotherserver$ mysql -utestuser -p -h testserver hoge
  
Enter password:
  
Welcome to the MySQL monitor. Commands end with ; or \g.
  
Your MySQL connection id is 8
  
Server version: 5.5.17 MySQL Community Server (GPL)

Copyright (c) 2000, 2011, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
  
affiliates. Other names may be trademarks of their respective
  
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show databases;
  
+-------+
  
| Database |
  
+-------+
  
| information_schema |
  
| hoge |
  
| test |
  
+-------+
  
3 rows in set (0.00 sec)

```

「testuser@'%'」の「%」はワイルドカードなので、localhostも含めた全てのホストを指すものかと思ったら、どうやらそれは間違いのようだ。

調べてみたところ、以下のような仕組み。

4.8.2 MySQL への新規ユーザの追加
  
 <a href="http://dev.mysql.com/doc/refman/5.1/ja/adding-users.html" target="_blank">http://dev.mysql.com/doc/refman/5.1/ja/adding-users.html</a>

> monty というユーザ名と some\_pass というパスワードのアカウントが 2つ存在します。どちらもフル権限を持つスーパーユーザのアカウントです。'monty'@'localhost') というアカウントは、ローカル ホストから接続するときにだけ使用できます。一方の 'monty'@'%' というアカウントは、どのホストからでも接続できます。注意： monty というアカウントは両方とも、monty としてどこからでも接続できる必要があります。この localhost でアカウントを持っていない場合、monty でローカル ホストから接続したときに、mysql\_install_db で作成している localhost のエントリで、匿名ユーザのアカウントとして優先になります。つまり、 monty が匿名ユーザとして扱われます。この理由は、'monty'@'%' よりも、匿名ユーザの方が具体的な Host カラム値にあるため、匿名の方が、user テーブルのソート順で先にきます。. (user テーブルのソートに関しては、項4.7.5. 「アクセス制御の段階 1： 接続確認」 を参照してください。)

分かったような、わからないような。参照先を見てみるとより詳しい説明がある。

4.7.5. アクセス制御の段階 1： 接続確認
  
 <a href="http://dev.mysql.com/doc/refman/5.1/ja/connection-access.html" target="_blank">http://dev.mysql.com/doc/refman/5.1/ja/connection-access.html</a>

長いので引用は避けるが、MySQLがuserテーブルに書かれた情報をどのようにソートして使用しているかが影響しているようだ。上のページの説明によると、以下の順でソートを行っている。

  1. 最も具体的なホスト名(つまり、%のように明示的でない指定は後回し)
  2. 最も具体的なユーザ名

つまり、はじめの方に出てきたユーザのテーブルは、実際には以下のように並んでいることになる。

```

+----+----+
  
| host | user |
  
+----+----+
  
| localhost | root |
  
| localhost | |
  
| 127.0.0.1 | root |
  
| % | testuser |
  
+----+----+

```

ログイン時のユーザ名とホスト名の組み合わせは、このようにソートしたテーブルを上から順に検索していき、最初に一致したもので認証が行われる。

ここで、「testuser」というユーザがlocalhostからアクセスしてくると、2行目の「localhostの匿名ユーザ(userが空白 = 匿名ユーザ)」に先に一致してしまうのである。
  
この裏付けに、パスワードなしでtestuserでログインできるようにしてみると、確かに実際には匿名ユーザとしてログインしてしまっていることがわかる。

```

$ mysql -utestuser

mysql> select current_user();
  
+------+
  
| current_user() |
  
+------+
  
| @localhost |
  
+------+
  
1 row in set (0.00 sec)

```

従って、「testuser@localhost」としてログインしたいなら、明示的に

```

mysql> grant select on hoge.* to testuser@'localhost' identified by 'password';

```

とも実行して、「localhostのtestuser」を作成する必要がある。

<span style="text-decoration: underline;">注 : セキュリティ上、ホストを「%」でワイルドカード指定するのはよろしくないので、やらないようにしましょう(汗)</span>

