---
id: 921
title: MySQLでテーブルの行数を数える
date: 2011-12-06T15:24:45+00:00
author: doublemarket
layout: post
guid: http://b.l0g.jp/?p=921
permalink: /mysql/counting-row-num/
categories:
  - MySQL
---

テーブル内の行数を数えるのに一番簡単なのは、

```
  
select count(*) from 'テーブル名';
  
```

と実行することだが、これだと最悪そのテーブルを総なめすることになる。
  
こういう時はinformation schemaから情報を引く。information\_schema.tablesテーブルのtable\_rowsカラムが行数。

```
  
mysql> select table\_schema, table\_name, table\_rows from information\_schema.tables
      
-> where table_schema = 'hoge';
  
+-------+-------------+----+
  
| table\_schema | table\_name | table_rows |
  
+-------+-------------+----+
  
| hoge | wp\_FollowMe\_Links | 2 |
  
| hoge | wp_commentmeta | 180 |
  
| hoge | wp_comments | 60 |
  
| hoge | wp_ktaisession | 0 |
  
| hoge | wp_links | 7 |
  
| hoge | wp_options | 236 |
  
| hoge | wp_postmeta | 509 |
  
| hoge | wp_posts | 323 |
  
| hoge | wp\_term\_relationships | 39 |
  
| hoge | wp\_term\_taxonomy | 9 |
  
| hoge | wp_terms | 9 |
  
| hoge | wp_usermeta | 31 |
  
| hoge | wp_users | 2 |
  
+-------+-------------+----+
  
```

showを使っても行数を引くことができるが、showの性質上表示させるカラムなどを細かく指定できず使いづらいこともあるので、information_schemaを引く方が好き。

```
  
show table status;
  
```

なおこれらのコマンドで引ける行数は、MyISAMなどのストレージエンジンでは正確な値だが、InnoDBの場合は概算値となる。なので、InnoDBの場合はselect count(*)するしかない。

* * *

**海外の役立つブログ記事などを人力で翻訳して公開する[Yakst](https://yakst.com/ja)というプロジェクトをやっています。よろしければそちらもどうぞ！**
