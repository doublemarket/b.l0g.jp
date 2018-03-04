---
id: 1644
title: 「SQLパフォーマンス詳解」という本を翻訳しました
date: 2015-04-07T08:45:15+00:00
author: doublemarket
layout: post
guid: http://b.l0g.jp/?p=1644
permalink: /uncategorized/sql-performance-explained-ja/
categories:
  - 未分類
---

&nbsp;

[<img class="alignleft wp-image-1648 size-medium" style="border: 0px;" src="http://b.l0g.jp/wp-content/uploads/2015/04/sqlperformanceexplainedja-196x300.png" alt="sqlperformanceexplainedja" width="196" height="300" />](http://b.l0g.jp/wp-content/uploads/2015/04/sqlperformanceexplainedja.png)題の通り、「<a title="SQLパフォーマンス詳解" href="http://sql-performance-explained.jp/" target="_blank">SQLパフォーマンス詳解</a>」(原文タイトル<a title="SQL Performance Explained" href="http://sql-performance-explained.com/" target="_blank">SQL Performance Explained</a>)という本を翻訳しました。PDF版と印刷版が上記サイトから購入できます。

(追記 2017年9月から、渋谷の[BOOK LAB TOKYO](http://booklabtokyo.com/)さんでも印刷版を販売していただいています。輸送コストの関係で、サイトから購入するより若干安くなっています)

リレーショナルデータベースにおいて、SQLとインデックスがどのように関連し、どのようにすればSQLのパフォーマンスを良くできるのかを解説した本です。特定のデータベース製品に焦点を当てた本は多数ありますが、この本ではOracle Database、PostgreSQL、MySQL、SQL Serverの4つのメジャーなリレーショナルデータベース製品を同時に扱っていて、それぞれのクセや特徴も分かるように書かれている点が非常にユニークになっています。また、ORMが生成するSQL文の違いなどにも言及されています。

前書きにもあるように、データへのアクセスパスを知っているのは他ならぬ開発者なので、開発者自身がSQLとインデックスについての知識を深めなければ本当の高速化はできない、というのが著者の考えのようですが、もちろんアプリ開発者だけでなくインフラエンジニア、データベースエンジニアの方々にもおすすめできる内容だと思います。

目次とそれぞれの内容は以下の通りです。

  * 第1章 インデックスの内部構造
      * インデックスとはそもそも何か、インデックスはどのような仕組みか
  * 第2章 where句
      * where句とインデックスの関連、例えば=を使った場合、likeを使った場合など演算子の違いによるインデックスの活用方法など
  * 第3章 パフォーマンスとスケーラビリティ
      * インデックスの作り方の違いによるパフォーマンスとスケーラビリティの違い
  * 第4章 結合処理
      * 結合(join)とインデックスの関連
  * 第5章 データのクラスタリング
      * データのクラスタリング(HAクラスタなどではなく、いわゆるクラスタリングインデックスのこと)
  * 第6章 ソートとグルーピング
      * ソート(order by)やグルーピング(group by)とインデックスの関連
  * 第7章 部分結果
      * MySQLで言うlimitやPostgreSQLのfetch firstのような、結果の一部分を取得するSQL文とインデックスの関連
  * 第8章 データの変更
      * update, insert, deleteといった更新処理とインデックスの関連
  * 付録A 実行計画
      * 各データベース製品での実行計画の表示方法とその読み方

既にご存知の方もいるかもしれませんが、<a title="Use The Index, Luke日本語版" href="http://use-the-index-luke.com/ja/sql/table-of-contents" target="_blank">Use The Index, Luke</a>という名でほぼ全文がWeb上で公開されています。とはいえWeb上で通しで読むのはかなり辛いですし、PDFあるいは印刷版を購入いただければ、作者・訳者とも嬉しい限りです。

私は、<a title="Yakst" href="http://yakst.com/ja" target="_blank">Yakst</a>というサイトで海外のブログ記事などを翻訳して公開するというのをやっているのですが、原著者(<a title="Markus Winand" href="https://twitter.com/markuswinand" target="_blank">Markus Winandさん</a>)が書かれた<a title="Yakst - SQLに対するMySQLと、NoSQLに対するMongoDBは似ている――主に有害な意味で" href="http://yakst.com/ja/posts/74" target="_blank">ブログ記事を翻訳させてもらった</a>のがきっかけで、この本の翻訳をやることになりました。オンライン販売だけの予定なので書店に本が並ぶわけではないとは言え、自分が訳した本が人に読まれることになるというのは、不思議な感覚です。Markusさんはオーストリア人で英語のネイティブスピーカーではないこともあり、原文は非常に分かりやすい英語で書かれているのですが、それでも翻訳の難しさ、自分の未熟さをひしひしと感じました。素晴らしい本を書いてくださった原著者のMarkusさん、私のつたない訳を丁寧にレビューしてくださった<a title="matsuu" href="https://twitter.com/matsuu" target="_blank">@matsuu</a>さん、ありがとうございました！

訳の問題など、ご意見ご感想ありましたら<a title="doublemarket" href="https://twitter.com/dblmkt" target="_blank">@dblmkt</a>までいつでもご連絡ください。

ちなみに、海外の有益な技術系ブログを翻訳して公開している<a title="Yakst" href="http://yakst.com/ja" target="_blank">Yakst</a>で、翻訳を一緒にやっていただける仲間も募集しておりますので、良かったらそちらも是非よろしくお願いします。
