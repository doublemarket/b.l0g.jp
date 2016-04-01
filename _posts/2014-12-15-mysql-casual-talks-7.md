---
id: 1625
title: MySQL Casual Talks Vol. 7まとめ
date: 2014-12-15T09:11:16+00:00
author: doublemarket
layout: post
guid: http://b.l0g.jp/?p=1625
permalink: /mysql/mysql-casual-talks-7/
categories:
  - MySQL
---

ふう、[MySQL Casual Talks Vol. 7](http://mysql-casual.connpass.com/event/9767/)行って参りました。前回は[自分が発表者](http://www.slideshare.net/doublemarket/mysqlcasual6fabric)でもあったので、正直なところ他の人の発表を聞く余裕はなく(汗) 今回は、同僚の発表もあったので楽しみにしていったら、相変わらずの濃ゆい話が盛りだくさんで、非常に楽しめました。毎度の事ながら、[@myfinderさん](https://twitter.com/myfinder)始め、会場提供・準備していただいたLINE
  
のみなさま、Oracleのみなさまに感謝です。

  * [Togetter](http://togetter.com/li/756928)

# [yoku0825さん](https://twitter.com/yoku0825) MySQL Fabricつらい



<div style="margin-bottom:5px">
  <strong> <a href="//www.slideshare.net/yoku0825/mysql-fabric-42641503" title="MySQL Fabricつらい" target="_blank">MySQL Fabricつらい</a> </strong> from <strong><a href="//www.slideshare.net/yoku0825" target="_blank">yoku0825</a></strong>
</div>

  * GAから半年なのに誰も使ってない
  * アメリカのイベントでは、MySQL Fabricだけに布がプレゼントされた(白目
  * Fabric対応コネクタが各種言語向けに用意されてる
  * [yokuさん謹製fabric対応mysqlクライアント](https://github.com/yoku0825/DBD-mysql/tree/support_mysql_fabric) 
      * fabricとConnector/C経由で通信する
      * ro(スレーブ、分散される)とrw(マスタ、当然1台)のモードがある
      * マスタを落とすとちゃんと切り替わりますよ(デモ)

# [tatsuruさん](https://twitter.com/tatsuru) MySQL on EC2

  * 3年ぐらい運用、結構消耗
  * MHA組んでおく 
      * ENIをつけておいて、マスタ切り替えはそれを付け替える
  * Mackerelでモニタリング
  * バックアップスレーブでsnapshotとってる
  * スレーブ作る 
      * snapshotから作る
      * dumpから作る
      * 落ちたら捨てる前提(信頼性落とす、ダウンしたら捨てる)
  * 取りあえず2セット作って入れ替える、が可能なのがよい
  * スレーブのオートスケールは結構難しい 
      * warmup難しい
      * 5.7はできそう(buffer poolリストア)
  * データはEBSに置いている 
      * IOできなくなって死ぬことがある
      * ヘルスチェック工夫しよう
  * RDSはちょっと高いけど楽 
      * フェイルオーバーが長い
      * 可用性がちょっと微妙でやめた(2012年ぐらいの話なので古いかも)
  * ELBはDNSベースなのでちょっとってことでhaproxyで分散している
  * もはや自前でMySQL運用する時代じゃないよね

# [ヤフー三谷さん @mita2](https://twitter.com/mita2) Percona Live行ってきた

  * Oracle(200セット), MySQL/Percona(300セット)を運用中 
      * Exadataとかも
  * Percona Live 10月にロンドンで開催されたのに参加してきた
  * XtraDB Cluster 
      * ActiveActiveの構成、高い冗長性
      * ヨーロッパでは広く普及
      * OpenStackのバックエンドに使われている
      * 本家のGroup Replicationと似てる
      * どのノードに書いても同期レプリされる
      * MySQL Clusterよりは本家MySQLに近くてハードル低い
      * Writeのスケールはできない(MySQL Cluster使おう)
      * SST(丸ごとコピー)、IST(差分)でDonorからデータを送る
      * ノードへの分散はHAProxyとか使う
      * ヘルスチェックの仕組みもXtraDB型に準備されてる
      * 書き込みは楽観的ロック
      * コミットしてノード間の情報が食い違ってるとコミット時にエラー
      * 従って書き込みはできるだけ1台がいい
      * オンラインalterはできない(今後サポート予定)ので、ローリングで変更していく

# [ペパボ okumuraさん @hfm](https://twitter.com/hfm) MySQL 4.0をリプレイスした



<div style="margin-bottom:5px">
  <strong> <a href="//www.slideshare.net/hifumis/mysql-casual-42634961" title="MySQL 4.0で9年動き続けたサーバを リプレイスしてバージョンアップした話" target="_blank">MySQL 4.0で9年動き続けたサーバを リプレイスしてバージョンアップした話</a> </strong> from <strong><a href="//www.slideshare.net/hifumis" target="_blank">Takahiro Okumura</a></strong>
</div>

  * [ブログ記事](http://blog.hifumi.info/2014/12/13/mysql-casual-vol-7/)
  * 2005年 4.0.25 -> 9年 -> 5.0.96にバージョンアップ
  * @tnmt 氏がカジュアルに5.6バージョンアップを指示するイシューを投げたところから始まった
  * しかしやる事になった時は何にも知識ありませんでした
  * たくさんの爆弾が仕込まれていた。。 
      * マスタとスレーブの食い違いなどなど
      * ハードもソフトも古い
  * 検証の過程で色んなバージョンをインストールしたので、MySQL-AllStarというbox提供してます(4.0から5.6まで全部入り)
  * 結局5.0まで上げることに
  * 6/20 イベントがあるとレンタルサーバのアクセスが減るからwという理由でメンテ実施 
      * ダンプからリストア、切り替えまで同じタイミングでやった
      * insertを実行するとbinlogが壊れることが発覚
      * 失敗として切り戻し
      * 5.0系を特定のgccでコンパイルすると起きるバグ？
      * gccの最適化のレベルによってテストが通ったり通らなかったり
  * その後別のバグ発生 
      * rpmbuild中に必要なファイルが消える
      * 邪魔してたパッケージ消してビルド成功
  * 8/22無事バージョンアップ成功
  * 4.0に入っていたデータの一部がリカバリできなかったのは手動で戻した

# [サイバーエージェント @strsk さん](https://twitter.com/strsk) ソーシャルゲームDBの危機回避



  * [ブログ記事](http://strsk.hatenablog.com/entry/2014/12/15/123211)
  * ギフトとかカードの情報が増えまくる 
      * 1日数十GB、論削だけで物理削除してなかったり
      * 削除するのではなく、必要なデータだけをINSERT SELECTで抽出してテーブルを作り直すと速い
      * deleteしてもテーブル容量減らない(MVCCのバージョン管理のため)
      * pt-online-schema-changeでテーブル再構築
  * レプリ遅延 
      * truncateすると遅れる(deleteより遅い)
      * バッファプールを大きく使ってると遅くなるというバグ
      * 結局deleteしました

# [@ryopeko さん](https://twitter.com/ryopeko) Q4M

  * [ブログ記事](http://qiita.com/ryopeko/items/f42b65a11d7f1f74311e)
  * パーフェクトRuby出しました
  * Rubyの人はRedis使う人多い(sidekiqで)けどQ4M 
      * [shinq](https://github.com/ryopeko/shinq)
      * ActiveJobを利用
      * Rails 4.2に入る予定のキュー機能

# [サイバーエージェント @kakerukaeru さん](https://twitter.com/kakerukaeru) continuous restore



  * [ブログ記事](http://kakerukaeru.hatenablog.com/entry/2014/12/13/213342)
  * DBのバックアップだけじゃなくてリストアも繰り返しやって、復旧可能性を担保
  * バァーン、バァーン、バババァーン

# [@ijin さん](https://twitter.com/ijin) ConsulでMySQLのフェイルオーバー



  * [元ネタのブログ記事](http://ijin.github.io/blog/2014/07/11/mysql-failover-with-consul/)
  * consulはraftを使ってcap定理のcpを実現している
  * MHA発動時に実行されるfailover scriptでconsulのAPIを叩いてマスタのDNS名を変更する
  * consul event 
      * eventをノードに伝えると伝播される(no guarantee)
      * watchで検知してスクリプトを動かす
  * consul templateを使いましょう

# [@kazeburo さん](https://twitter.com/kazeburo) isuconのためのMySQLチューニング



<div style="margin-bottom:5px">
  <strong> <a href="//www.slideshare.net/kazeburo/mysql-casual7isucon" title="ISUCON4 予選問題で(中略)、”my.cnf”に1行だけ足して予選通過ラインを突破するの術" target="_blank">ISUCON4 予選問題で(中略)、”my.cnf”に1行だけ足して予選通過ラインを突破するの術</a> </strong> from <strong><a href="//www.slideshare.net/kazeburo" target="_blank">Masahiro Nagano</a></strong>
</div>

  * my.cnfをチューニングする 
      * innodb\_flush\_log\_at\_trx_commit=2で結構上がる
      * innodb\_flush\_method=nosync/O_DIRECTでも上がる
      * この2つのオプションさえちゃんとやっておけばおk
      * データ量に応じてinnodb\_buffer\_pool_sizeも

# [@neofact さん](https://twitter.com/neofact)、 [@kuwa_tw さん](https://twitter.com/kuwa_tw) NVMFS



<div style="margin-bottom:5px">
  <strong> <a href="//www.slideshare.net/akuwano/ca-nvmfs-42665725" title="NVMFS 使ってみたとか 言っちゃって マジカジュアルな奴" target="_blank">NVMFS 使ってみたとか 言っちゃって マジカジュアルな奴</a> </strong> from <strong><a href="//www.slideshare.net/akuwano" target="_blank">Akihiro Kuwano</a></strong>
</div>

  * [ブログ記事](http://d.hatena.ne.jp/akuwano/20141215)
  * 圧縮効いて8〜6割の性能
  * 容量的には半分ぐらいに圧縮できた
  * 圧縮伸長でCPU食う(元々ioDriveではCPUバウンドだけどさらにきつくなる)

# [@do_aki さん](https://twitter.com/do_aki) N:1レプリケーション

  * いつもの
  * 特に進捗なし
  * Raspberry piでN:1 replication動きましたw

# [@kamipo さん](https://twitter.com/kamipo) ActiveRecord

  * [発表資料](http://kamipo.github.io/talks/20141212-mysqlcasual7/#/title)

  * ARのメンテナはみんなPostgreSQL派で、MySQLのプルリクは放置される傾向にある

  * [いっぱいMySQLのプルリク投げた！](https://github.com/rails/rails/pulls/kamipo) 
      * For MySQLってつけたの全部放置されてるorz
      * 機能をバックポートして[activerecord-mysql-awesome作った](http://qiita.com/kamipo/items/11b55caf329210923a9a)
  * kamipoさんはすごいひと

* * *

**海外の役立つブログ記事などを人力で翻訳して公開する[Yakst](https://yakst.com/ja)というプロジェクトをやっています。よろしければそちらもどうぞ！**
