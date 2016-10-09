---
id: 1530
title: MySQL Cluster Casual Talksメモ
date: 2013-09-27T09:25:33+00:00
author: doublemarket
layout: post
guid: http://b.l0g.jp/?p=1530
permalink: /mysql/mysql-cluster-casual-talks-memo/
categories:
  - MySQL
---

**<a href="http://atnd.org/events/42911" target="_blank">MySQL Cluster Casual Talks</a>**

一応仕事でMySQLをよく触っている身として、軽く検証はしてみたものの、担当している業務では参照メインのDBが多いこともあって実運用では使ったことがなかったMySQL Cluster。改めて利点も欠点も聞いておきたいと思ったのと、会社からすぐ近くのGMOさんが会場ということもあって参加してみた。

MySQL Clusterの本も書かれている@nippondanjiこと奥野さんから、MySQL Clusterは何に向いていて、どう使えばよさを引き出せるのかを聞いたうえで、そのサポートを受けながら4年間運用されている@tsakuradaさんの実運用上のハマりどころを聞くことで、どういうところにMySQL Clusterを使えばいいのかイメージが浮かんだので、非常に今後の役に立ちそう。

発表者のみなさま、会場提供していただいたGMOのみなさまありがとうございましたm(__)m さくらのクラウド2万円分クーポンももらったし、GMOクラウドのキャラクタのシールもたくさんもらったので宣伝しますｗ

既にまとめは<a href="http://twitter.com/76whizkidz" target="_blank">garage-kid</a>さんが<a href="http://d.hatena.ne.jp/garage-kid/20130926/mysqlclustercasualtalks" target="_blank">書いてくれている</a>ので、これは個人的なポイントのまとめ。

## <a href="http://twitter.com/RKajiyama" target="_blank">@RKajiyama</a>さん MySQL Cluster大地に立つ！「5.6とは違うのだよ、5.6とは！」

  * MySQL Clusterの概要 
      * 分散型、共有ディスク不要のHAクラスタ
  * 使用しているユーザ 
      * 元々はモバイル通信の位置情報をリアルタイムかつ高可用性を持って保存するためにできた
      * SQLインタフェースは昨今ゲーム業界などで使われ始めてから追加されてきた機能
      * 艦○れでも使われている？

## <a href="http://twitter.com/nippondanji" target="_blank">@nippondanji</a>さん カジュアルにMySQL Clusterを使ってみよう

<div style="margin-bottom:5px">
  <strong> <a href="https://www.slideshare.net/nippondanji/mcct" title="カジュアルにMySQL Clusterを使ってみよう@MySQL Cluster Casual Talks 2013.09" target="_blank">カジュアルにMySQL Clusterを使ってみよう@MySQL Cluster Casual Talks 2013.09</a> </strong> from <strong><a href="http://www.slideshare.net/nippondanji" target="_blank">Mikiya Okuno</a></strong>
</div>

データノード、SQLノード、管理ノードの3種類からなる

  * 1台だけならndb(MySQL Clusterのエンジン)よりInnoDBの方が速い
  * ただしNDB APIを使うならClusterの方が圧倒的に速い
  * ノードを増やすとスケールしていく点に優位性があるが、いつでも速くなるとは限らない(後述)
  * 新しいバージョン使いましょう

シェアードナッシング型

  * データノードは2台ペアでノードグループを構成
  * データをフラグメントに分けて、それをノードグループ内で同期レプリする
  * RAID10的な感じでグループ内で1台落ちてもデータは保全される

InnoDBとは特性が違う & いつでもノードを増やせばスケールするとは限らない

  * 主キーを使った検索 → データノード数に応じてスケール
  * **(少数の行を取ってくる)スキャンは苦手** 
      * データがノードに分散しているので複数ノードをスキャンする必要があるため
      * フェッチするレコードが多いと並列処理できるので早くなる
      * Engine Condition Pushdown : where句部分を丸ごとデータノードに渡して並列処理して高速化する仕組み
      * sysbenchをただ走らせると遅いのはこのせい
  * ユーザ定義パーティション 
      * スキャンを高速化させる仕組み
      * 2つのテーブルの関連を考慮してデータノードへの分散を定義すれば、読み書きを特定のノードに抑えられるので高速に
  * レプリケーション 
      * **ndbに不得意な処理があるならInnoDB**のスレーブを作るという手もある
      * データノードが書き込みしたことをSQLノードに通知し、それをbinlogに書き込んでスレーブに送るという仕組み
      * 通知を受け取るのがbinlog injector thread
  * **インメモリDBだけど永続化処理が多いのでディスク書き込み多い＝速いディスクがいい**
  * **blobを別テーブルに保存するのでJOIN的処理が発生して遅くなる** 
      * varcharで頑張ろう
  * NoSQLインタフェースを使えば爆速 
      * 各種言語のインタフェースあり
      * InnoDBのmemcachedインタフェースよりMySQL Clusterの方が現時点では賢い

まとめ(MySQL Clusterの使いどころ)

  * 大量の更新をさばきたい
  * HA機能が欲しい
  * 高速JOINしたい

## <a href="http://twitter.com/tsakurada" target="_blank">@tsakurada</a>さん MySQL Clusterと丸４年の付き合いを振り返ってのよもやま話と、Version 7.3への期待（もっとユーザが増えるといいな～）



<div style="margin-bottom: 5px;">
  <strong> <a title="MCCT20130926 tsakuradac" href="https://www.slideshare.net/TakeshiSakurada/mcct20130926-tsakuradac" target="_blank">MCCT20130926 tsakuradac</a> </strong> from <strong><a href="http://www.slideshare.net/TakeshiSakurada" target="_blank">Takeshi Sakurada</a></strong>
</div>

使用開始時の期待と使ってみたギャップ、現在の期待

  * インメモリだから速いはず 
      * **参照に関していえばFIO+InnoDBでも変わらないかも**
  * スケールアウト簡単 
      * **構成によってはスケールしない**こともあるので要考慮
  * 高可用性 
      * 期待通り、フェイルオーバーは数秒で完了
  * ライセンスがGPLでオープンソース
  * 7.3でInnoとの互換性が上がって期待

サーバの構成

  * SQLノードと管理ノードは同居してもよい、データノード2台と合わせて最低4台からが現実的
  * データが多いならデータノードを増やす(が制約はあるので注意)
  * アクセスが多いならSQLノードを増やす。制約は少ないので割と簡単
  * せっかくのMySQL ClusterなんだからHW構成上もSPOFを減らそう
  * **クエリがキャンセルされた時それを検出して後続処理をやるアプリ作り込み必須** 
      * ノード障害時にクエリがキャンセルされるため

InnoDBとの組み合わせ

  * Innoと同じ感覚でテーブル設計してもよいか？ 
      * 場合による。最近のバージョンではやってみる価値あり
  * InnoDBスレーブとの組み合わせ 
      * 分離レベルが違う点は要注意だがおおむね問題なし

ハマりどころ(資料が詳しいのでそちら参照)

  * GCP Stop
  * ローリングリスタート
  * ボトルネックが見えづらい
  * データのライフサイクルを考えよう
  * リストアが大変

4年間でだいぶ地雷を踏んだのでもう大丈夫ｗ 皆さんMySQL Cluster使いましょう

## <a href="http://twitter.com/yyamasaki1" target="_blank">@yyamasaki1</a>さん MySQL Cluster Auto-Installerのデモ

<div style="margin-bottom:5px">
  <strong> <a href="https://www.slideshare.net/yoyamasaki/5mysql-cluster" title="5分で作るMySQL Cluster環境" target="_blank">5分で作るMySQL Cluster環境</a> </strong> from <strong><a href="http://www.slideshare.net/yoyamasaki" target="_blank">yoyamasaki</a></strong>
</div>

Auto-Installer簡単なので使いましょう

  * ブラウザベースでぽちぽちやればインストール可能
  * 初期パラメータをいくつかいじった方がいい

## <a href="http://twitter.com/yoku0825" target="_blank">@yoku0825</a>さん ウチがNDBCLUSTERを使わない理由をもう一度考える

MySQL Clusterというと紛らわしいので、NDB Clusterと言いましょう<table border=0> 

</table> 

