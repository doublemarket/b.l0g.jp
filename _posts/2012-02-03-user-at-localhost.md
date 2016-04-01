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
<div class='wp_social_bookmarking_light'>
  <div class="wsbl_hatena_button">
    <a href="http://b.hatena.ne.jp/entry/http://b.l0g.jp/mysql/user-at-localhost/" class="hatena-bookmark-button" data-hatena-bookmark-title="MySQL ユーザのホストをワイルドカードで指定してもlocalhostは含まれない" data-hatena-bookmark-layout="standard" title="このエントリーをはてなブックマークに追加"> <img src="//b.hatena.ne.jp/images/entry-button/button-only@2x.png" alt="このエントリーをはてなブックマークに追加" width="20" height="20" style="border: none;" /></a>
  </div>
  
  <div class="wsbl_facebook_like">
    <div id="fb-root">
    </div><fb:like href="http://b.l0g.jp/mysql/user-at-localhost/" layout="button_count" action="like" width="100" share="false" show_faces="false" ></fb:like>
  </div>
  
  <div class="wsbl_twitter">
    <a href="https://twitter.com/share" class="twitter-share-button"{count} data-url="http://b.l0g.jp/mysql/user-at-localhost/" data-text="MySQL ユーザのホストをワイルドカードで指定してもlocalhostは含まれない" data-via="dblmkt " data-lang="ja">Tweet</a>
  </div>
  
  <div class="wsbl_google_plus_one">
    <g:plusone size="medium" annotation="none" href="http://b.l0g.jp/mysql/user-at-localhost/" ></g:plusone>
  </div>
</div>

<br class='wp_social_bookmarking_light_clear' />

MySQLをインストールしたので、とりあえずワイルドカードでホスト名を指定してテスト用のユーザを作成。

[sql]

mysql> show databases;
  
+&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8211;+
  
| Database |
  
+&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8211;+
  
| information_schema |
  
| hoge |
  
| mysql |
  
| performance_schema |
  
| test |
  
+&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8211;+
  
5 rows in set (0.00 sec)

mysql> grant select on hoge.* to testuser@&#8217;%&#8217; identified by &#8216;password&#8217;;
  
Query OK, 0 rows affected (0.07 sec)

mysql> select host,user from mysql.user;
  
+&#8212;&#8212;&#8212;&#8211;+&#8212;&#8212;&#8212;-+
  
| host | user |
  
+&#8212;&#8212;&#8212;&#8211;+&#8212;&#8212;&#8212;-+
  
| % | testuser |
  
| 127.0.0.1 | root |
  
| localhost | |
  
| localhost | root |
  
+&#8212;&#8212;&#8212;&#8211;+&#8212;&#8212;&#8212;-+
  
7 rows in set (0.00 sec)

mysql> quit

[/sql]

この状態で、MySQLがインストールされているサーバ自体からそのままtestuserでログインしようとすると、アクセスが拒否される。

[sql]

testserver$ mysql -utestuser -p
  
Enter password:
  
ERROR 1045 (28000): Access denied for user &#8216;testuser&#8217;@&#8217;localhost&#8217; (using password: YES)

[/sql]

しかし、他のサーバからアクセスしてみると、接続は成功して、hogeデータベースへのアクセスもできる。

[sql]

anotherserver$ mysql -utestuser -p -h testserver hoge
  
Enter password:
  
Welcome to the MySQL monitor. Commands end with ; or \g.
  
Your MySQL connection id is 8
  
Server version: 5.5.17 MySQL Community Server (GPL)

Copyright (c) 2000, 2011, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
  
affiliates. Other names may be trademarks of their respective
  
owners.

Type &#8216;help;&#8217; or &#8216;\h&#8217; for help. Type &#8216;\c&#8217; to clear the current input statement.

mysql> show databases;
  
+&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8211;+
  
| Database |
  
+&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8211;+
  
| information_schema |
  
| hoge |
  
| test |
  
+&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8211;+
  
3 rows in set (0.00 sec)

[/sql]

「testuser@&#8217;%&#8217;」の「%」はワイルドカードなので、localhostも含めた全てのホストを指すものかと思ったら、どうやらそれは間違いのようだ。

調べてみたところ、以下のような仕組み。

4.8.2 MySQL への新規ユーザの追加
  
 <a href="http://dev.mysql.com/doc/refman/5.1/ja/adding-users.html" target="_blank">http://dev.mysql.com/doc/refman/5.1/ja/adding-users.html</a>

> monty というユーザ名と some\_pass というパスワードのアカウントが 2つ存在します。どちらもフル権限を持つスーパーユーザのアカウントです。&#8217;monty&#8217;@&#8217;localhost&#8217;) というアカウントは、ローカル ホストから接続するときにだけ使用できます。一方の &#8216;monty&#8217;@&#8217;%&#8217; というアカウントは、どのホストからでも接続できます。注意： monty というアカウントは両方とも、monty としてどこからでも接続できる必要があります。この localhost でアカウントを持っていない場合、monty でローカル ホストから接続したときに、mysql\_install_db で作成している localhost のエントリで、匿名ユーザのアカウントとして優先になります。つまり、 monty が匿名ユーザとして扱われます。この理由は、&#8217;monty&#8217;@&#8217;%&#8217; よりも、匿名ユーザの方が具体的な Host カラム値にあるため、匿名の方が、user テーブルのソート順で先にきます。. (user テーブルのソートに関しては、項4.7.5. 「アクセス制御の段階 1： 接続確認」 を参照してください。)

分かったような、わからないような。参照先を見てみるとより詳しい説明がある。

4.7.5. アクセス制御の段階 1： 接続確認
  
 <a href="http://dev.mysql.com/doc/refman/5.1/ja/connection-access.html" target="_blank">http://dev.mysql.com/doc/refman/5.1/ja/connection-access.html</a>

長いので引用は避けるが、MySQLがuserテーブルに書かれた情報をどのようにソートして使用しているかが影響しているようだ。上のページの説明によると、以下の順でソートを行っている。

  1. 最も具体的なホスト名(つまり、%のように明示的でない指定は後回し)
  2. 最も具体的なユーザ名

つまり、はじめの方に出てきたユーザのテーブルは、実際には以下のように並んでいることになる。

[sql]

+&#8212;&#8212;&#8212;&#8211;+&#8212;&#8212;&#8212;-+
  
| host | user |
  
+&#8212;&#8212;&#8212;&#8211;+&#8212;&#8212;&#8212;-+
  
| localhost | root |
  
| localhost | |
  
| 127.0.0.1 | root |
  
| % | testuser |
  
+&#8212;&#8212;&#8212;&#8211;+&#8212;&#8212;&#8212;-+

[/sql]

ログイン時のユーザ名とホスト名の組み合わせは、このようにソートしたテーブルを上から順に検索していき、最初に一致したもので認証が行われる。

ここで、「testuser」というユーザがlocalhostからアクセスしてくると、2行目の「localhostの匿名ユーザ(userが空白 = 匿名ユーザ)」に先に一致してしまうのである。
  
この裏付けに、パスワードなしでtestuserでログインできるようにしてみると、確かに実際には匿名ユーザとしてログインしてしまっていることがわかる。

[sql]

$ mysql -utestuser

mysql> select current_user();
  
+&#8212;&#8212;&#8212;&#8212;&#8212;-+
  
| current_user() |
  
+&#8212;&#8212;&#8212;&#8212;&#8212;-+
  
| @localhost |
  
+&#8212;&#8212;&#8212;&#8212;&#8212;-+
  
1 row in set (0.00 sec)

[/sql]

従って、「testuser@localhost」としてログインしたいなら、明示的に

[sql]

mysql> grant select on hoge.* to testuser@&#8217;localhost&#8217; identified by &#8216;password&#8217;;

[/sql]

とも実行して、「localhostのtestuser」を作成する必要がある。

<span style="text-decoration: underline;">注 : セキュリティ上、ホストを「%」でワイルドカード指定するのはよろしくないので、やらないようにしましょう(汗)</span>

* * *

**海外の役立つブログ記事などを人力で翻訳して公開する[Yakst](https://yakst.com/ja)というプロジェクトをやっています。よろしければそちらもどうぞ！**