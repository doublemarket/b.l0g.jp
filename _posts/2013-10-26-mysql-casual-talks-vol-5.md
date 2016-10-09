---
id: 1555
title: MySQL Casual Talks vol.5 メモ
date: 2013-10-26T14:06:50+00:00
author: doublemarket
layout: post
guid: http://b.l0g.jp/?p=1555
permalink: /mysql/mysql-casual-talks-vol-5/
categories:
  - MySQL
---

&nbsp;

[MySQL Casual Talks vol. 5](http://www.zusaar.com/event/1086003)に行ってきたので、自分なりのメモと資料へのリンク。

途中、アラートが鳴り始めて確認してたりしてメモが適当なところがあるが仕方なしｗ

かなりのスピードでじゃんじゃんプレゼンが進んで行ったが、どれも面白い・役立つ内容ばかりで、楽しいひと時でした。発表者の皆様、会場の提供や調整などしてくださった<a href="https://twitter.com/myfinder" target="_blank">@myfinder</a>さんはじめとする皆様、ありがとうございました。自分の隣でビール缶を次々と「カシュッ」と空けていた<a href="https://twitter.com/oranie" target="_blank">@oranie</a>さんのような余裕を持ちつつ、次回は自分も何かネタを提供できるといいなあ。

## <a href="https://twitter.com/yuryu" target="_blank">@yuryu</a>さん GTIDを使い始めてやめた話



<div style="margin-bottom: 5px;">
  <strong> <a title="MySQL5.6でGTIDを試してそっと閉じた" href="https://www.slideshare.net/Yuryu/mysql56gtid" target="_blank">MySQL5.6でGTIDを試してそっと閉じた</a> </strong> from <strong><a href="http://www.slideshare.net/Yuryu" target="_blank">Haruka Iwao</a></strong>
</div>

  * GTID - 全てのトランザクションを一意に識別、一貫性保証
  * マスタを切り替えると、マスタのUUIDが変わるのでGTIDが2個になる
  * 利点 
      * change master to … master\_auto\_position=1で簡単にレプリをはれる
      * mysql workbenchのmysqlfailoverとか使える
  * 欠点 
      * クラスタ全部GTID有効にする必要
      * MyISAM使用不可
      * HAしたいならMHAでいいんじゃね？
  * バグ 
      * GTID有効でFLUSH LOGSするとレプリ停止(5.6.11のみ)
      * stop/start slaveするとトランザクションがスキップされる可能性あり
      * ネットワーク切断でトランザクションスキップの可能性
  * 結局、本格的に使うには時期尚早なので辞めました、ということ

## <a href="https://twitter.com/con_mame" target="_blank">@con_mame</a>さん MySQL 5.6移行記



<div style="margin-bottom: 5px;">
  <strong> <a title="僕らのMySQL5.6移行記(仮)" href="https://www.slideshare.net/conmame/mysql56-27565355" target="_blank">僕らのMySQL5.6移行記(仮)</a> </strong> from <strong><a href="http://www.slideshare.net/conmame" target="_blank">Yutaka Hoshino</a></strong>
</div>

  * クックパッでは5.5のサーバを5.6にバージョンアップしたり最初から使ったり
  * レプリしてクエリ流してコンフィグいじり始めた 
      * うまく行ったのは最初だけで問題勃発
  * EBSでスナップショット取ったボリュームを5.6マシンにぶら下げてmysql_upgrade実行
  * <a href="https://github.com/cookpad/kage" target="_blank">Kage</a> 
      * リクエストをコピーしてsandbox環境に流す仕組み(ユーザへはproductionからの結果が返る)
      * レスポンスのdiffも取れる
      * バックエンドを5.6にして違いを確認
  * バージョン違い、設定違いのサーバを並べて比較
  * 開発用DBを先に新しくしてリリース前に新環境を試す
  * バグ確認用(最新) → 検証用(1つ前) → 本番用(安定版)
  * 各バージョンが入ったAMIを用意しておいてすぐ起動できるようにしてある
  * GTID有効だとsql\_slave\_skip_counter使えない
  * AWSだと色んな検証がCasualにできる
  * <a href="http://bugs.mysql.com/" target="_blank">bugs</a>は見ましょう！
  * 5.6で遅くなるところもあるので、得意な部分から本番投入していっている

## <a href="https://twitter.com/yoshi_ken" target="_blank">@yoshi_ken</a>さん 5.0 Tritonnから5.6 mroongaへの移行

<div style="margin-bottom:5px">
  <strong> <a href="https://www.slideshare.net/y-ken/mysql-migrate-mysql50-to-mysql56-with-fulltext" title="Tritonn (MySQL5.0.87+Senna)からの mroonga (MySQL5.6) 移行体験記" target="_blank">Tritonn (MySQL5.0.87+Senna)からの mroonga (MySQL5.6) 移行体験記</a> </strong> from <strong><a href="http://www.slideshare.net/y-ken" target="_blank">Kentaro Yoshida</a></strong>
</div>

  * [技評の記事](http://gihyo.jp/dev/clip/01/groonga/0006)の裏話
  * 旧構成 マスタはDRBDでミラー、スレーブにレプリ
  * 5.6スレーブを追加してそこに孫をぶら下げて旧環境を切り離し
  * 本番環境のアクセスログから抜き出したURLを使ってテスト 
      * 5.6で遅くなったクエリをチューニング
      * <a href="http://d.hatena.ne.jp/oranie/20130402/1364906656" target="_blank">SQLMODE厳格化</a>への対応
  * テスト中に見つけたmroongaのバグはすぐに修正してもらえた
  * HWトラブル 
      * マスタ死亡、起動しない、焦げ臭い！さらにもう一台も焦げた。。
  * GTIDよりsemi sync repl + MHAがいいのでわ
  * innodb\_file\_per_tableでなかったのでダンプしてバージョンアップした
  * memcached APIはバグで使えなかった

## <a href="https://twitter.com/yoku0825" target="_blank">@yoku0825</a>さん アプリ担当が出してきたDDLがドイヒーな話

<div style="margin-bottom:5px">
  <strong> <a href="https://www.slideshare.net/yoku0825/ss-27597161" title="とあるイルカのバーボンハウス" target="_blank">とあるイルカのバーボンハウス</a> </strong> from <strong><a href="http://www.slideshare.net/yoku0825" target="_blank">yoku0825</a></strong>
</div>

  * すごい心が痛い&共感出来る内容だったけどテンポ早すぎてメモできなかったｗ 
  * MariaDB 10からもGTIDが使えるようになった(ただしMySQLとは結構違う)
  * ログ用テーブル 
      * 突っ込みどころたくさんww
  * インデックス名はidx\_カラム名\_カラム名_…とすると長いけどexplainのときに分かりやすい
  * フレームワークが作る通りではなくDBの特性を考える
  * SQL文を見るだけでどのインデックスを使うかなどの意図が分かる名前付け

## <a href="https://twitter.com/kamipo" target="_blank">@kamipo</a>さん mysql-build

<a href="http://kamipo.github.io/talks/20131025-mysqlcasual5/" target="_blank">プレゼン資料</a>

  * この辺りトラブル発生してメモがおろそか
  * いろんなMySQLをインストールして使い分ける仕組み
  * percona serverのmysqldumpにはkeyを消してダンプして作り直すオプションがある 
      * ダンプが高速化
  * fbの5.6にはlogical key readのオプションがある

## <a href="https://twitter.com/do_aki" target="_blank">@do_aki</a>さん multi master replication



<div style="margin-bottom: 5px;">
  <strong> <a title="殿堂入りのアレ～Multi-Source Replication を添えて～" href="https://www.slideshare.net/do_aki/ss-27565738" target="_blank">殿堂入りのアレ～Multi-Source Replication を添えて～</a> </strong> from <strong><a href="http://www.slideshare.net/do_aki" target="_blank">do_aki</a></strong>
</div>

  * MySQL Casual定番の内容
  * change masterを2秒ごとに切り替えてマルチマスタ
  * 5.7では標準でできるようになった
  * 過去に色々な方法でマルチマスタを試みている人がいるww

## <a href="https://twitter.com/RKajiyama" target="_blank">@RKajiyama</a>さん 5.7 Multi-source replication

<div style="margin-bottom:5px">
  <strong> <a href="https://www.slideshare.net/rkajiyama/20131025-casual-uppt" title="MySQL Casual Talks vol.5 - MySQL Labs - @RKajiyama" target="_blank">MySQL Casual Talks vol.5 - MySQL Labs - @RKajiyama</a> </strong> from <strong><a href="http://www.slideshare.net/rkajiyama" target="_blank">rkajiyama</a></strong>
</div>

  * labsにはDMR以前のものがありますよ 
      * hadoop applier for mysql 
          * hadoopをMySQLスレーブのように使う
      * JSON UDF
      * Multi source replication
      * MySQL Utilities Fabric 
          * シャーディングの情報を管理する仕組み
      * Fabric + multi source replication + multi thread slaveの組み合わせ良さげ

## <a href="https://twitter.com/songmu" target="_blank">@songmu</a>さん

<a href="http://songmu.github.io/slides/mysql-casual-5/" target="_blank">プレゼン資料</a>

  * GitDDL::Migrator, SQLTranslator(SQLFairy) 
      * ブランチごとにテーブルやカラムが違うと大変
      * テーブル構成の違いなどが表示できる
      * ブランチを行ったり来たりしながらalterしたりできる
  * App::RunCron 
      * perlのラッパー、レポータを書くと色々できる
      * cronのログってnullに捨てちゃうけど困るよね
      * cronlog
      * cronの処理をラップしてエラー処理が可能

## <a href="https://twitter.com/nekogeruge_987" target="_blank">@nekogeruge_987</a>さん DB2からMySQL5.5への移行



  * 13年運用されてるデータベース
  * レコードをCSVにしてload infile
  * シェルスクリプトで自動化
  * 4時間以内に終わらせるという時間制約 
      * loadを高速化する工夫
      * ジョブフローの最適化(jenkins build flow plugin)
      * ジョブフローをGroovyで書ける(並列実行などなども可)
      * データ移行自体をCIする(1クリックデータ移行！)
      * /var/lib/mysql以下の並列rsync

## <a href="https://twitter.com/monry" target="_blank">@monry</a>さん Amazon RDS



<div style="margin-bottom: 5px;">
  <strong> <a title="20131025 my sql casual talks vol.5" href="https://www.slideshare.net/monry84/20131025-my-sql-casual-talks-vol5" target="_blank">20131025 my sql casual talks vol.5</a> </strong> from <strong><a href="http://www.slideshare.net/monry84" target="_blank">Tetsuya Mori</a></strong>
</div>

  * マスタ1台、スレーブ5台 
      * 243ドル/月

  * 物理サーバが1台13万以上ならRDSの方が安い(3年償却)

## <a href="https://twitter.com/chobi_e" target="_blank">@chobi_e</a>さん ストレージエンジンを作ってみた



  * <a href="https://twitter.com/kazuho" target="_blank">kazuho</a>さんの<a href="http://d.hatena.ne.jp/kazuhooku/20131001/1380595265" target="_blank">リアルタイムランキング用MySQLストレージエンジンの話</a> 
      * 本を読む
      * <a href="http://www.amazon.co.jp/gp/product/B00ACC6ANM/ref=as_li_ss_tl?ie=UTF8&camp=247&creative=7399&creativeASIN=B00ACC6ANM&linkCode=as2&tag=l0gjp-22" rel="nofollow">Expert MySQL</a><img style="border: none !important; margin: 0px !important;" alt="" src="http://ir-jp.amazon-adsystem.com/e/ir?t=l0gjp-22&l=as2&o=9&a=B00ACC6ANM" width="1" height="1" border="0" />
  
        のChapter 10
      * <a href="http://www.amazon.co.jp/gp/product/1849510601/ref=as_li_ss_tl?ie=UTF8&camp=247&creative=7399&creativeASIN=1849510601&linkCode=as2&tag=doublemarket-22" rel="nofollow">Mysql 5.1 Plugin Development</a><img style="border: none !important; margin: 0px !important;" alt="" src="http://ir-jp.amazon-adsystem.com/e/ir?t=doublemarket-22&l=as2&o=9&a=1849510601" width="1" height="1" border="0" />
      * ランキングと言えばredis 
          * sorted setのソースを取ってくる
      * 意外と簡単に書けるので書いてみよう

