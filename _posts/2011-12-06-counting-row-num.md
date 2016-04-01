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
<div class='wp_social_bookmarking_light'>
  <div class="wsbl_hatena_button">
    <a href="http://b.hatena.ne.jp/entry/http://b.l0g.jp/mysql/counting-row-num/" class="hatena-bookmark-button" data-hatena-bookmark-title="MySQLでテーブルの行数を数える" data-hatena-bookmark-layout="standard" title="このエントリーをはてなブックマークに追加"> <img src="//b.hatena.ne.jp/images/entry-button/button-only@2x.png" alt="このエントリーをはてなブックマークに追加" width="20" height="20" style="border: none;" /></a>
  </div>
  
  <div class="wsbl_facebook_like">
    <div id="fb-root">
    </div><fb:like href="http://b.l0g.jp/mysql/counting-row-num/" layout="button_count" action="like" width="100" share="false" show_faces="false" ></fb:like>
  </div>
  
  <div class="wsbl_twitter">
    <a href="https://twitter.com/share" class="twitter-share-button"{count} data-url="http://b.l0g.jp/mysql/counting-row-num/" data-text="MySQLでテーブルの行数を数える" data-via="dblmkt " data-lang="ja">Tweet</a>
  </div>
  
  <div class="wsbl_google_plus_one">
    <g:plusone size="medium" annotation="none" href="http://b.l0g.jp/mysql/counting-row-num/" ></g:plusone>
  </div>
</div>

<br class='wp_social_bookmarking_light_clear' />

テーブル内の行数を数えるのに一番簡単なのは、

[sql]
  
select count(*) from &#8216;テーブル名&#8217;;
  
[/sql]

と実行することだが、これだと最悪そのテーブルを総なめすることになる。
  
こういう時はinformation schemaから情報を引く。information\_schema.tablesテーブルのtable\_rowsカラムが行数。

[sql]
  
mysql> select table\_schema, table\_name, table\_rows from information\_schema.tables
      
-> where table_schema = &#8216;hoge&#8217;;
  
+&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8211;+&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;+&#8212;&#8212;&#8212;&#8212;+
  
| table\_schema | table\_name | table_rows |
  
+&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8211;+&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;+&#8212;&#8212;&#8212;&#8212;+
  
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
  
+&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8211;+&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;+&#8212;&#8212;&#8212;&#8212;+
  
[/sql]

showを使っても行数を引くことができるが、showの性質上表示させるカラムなどを細かく指定できず使いづらいこともあるので、information_schemaを引く方が好き。

[sql]
  
show table status;
  
[/sql]

なおこれらのコマンドで引ける行数は、MyISAMなどのストレージエンジンでは正確な値だが、InnoDBの場合は概算値となる。なので、InnoDBの場合はselect count(*)するしかない。

* * *

**海外の役立つブログ記事などを人力で翻訳して公開する[Yakst](https://yakst.com/ja)というプロジェクトをやっています。よろしければそちらもどうぞ！**