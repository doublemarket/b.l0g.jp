---
id: 1096
title: MySQLの監視はCacti+Percona Monitoring Pluginsがおすすめ(監視対象設定編)
date: 2012-05-28T12:00:12+00:00
author: doublemarket
layout: post
guid: http://b.l0g.jp/?p=1096
permalink: /mysql/use-cacti-to-monitor-mysql-2/
categories:
  - Cacti
  - MySQL
---

<a title="MySQLの監視はCacti+Percona Monitoring Pluginsがおすすめ(監視サーバ構築編)" href="http://b.l0g.jp/mysql/install-cacti-to-monitor-mysql-1/" target="_blank">前回のエントリ</a>ではMySQLのサーバを監視できるように、CactiをインストールしてPercona Monitoring Pluginsのテンプレートを読み込んだところまで書いた。今回は、監視対象となるMySQLサーバを設定して、リソース監視を行うまでを書いてみる。なお、プロンプトがtargetとなって いるものが監視対象への設定で、serverとなっているものが監視サーバへの設定を意味している。

## SNMPの設定(Ubuntu serverの場合)

Ubuntuでは標準ではSNMPがインストールされていないので、まずはインストールする。

[bash]
  
target$ sudo apt-get install snmp snmpd
  
[/bash]

/etc/snmp/snmpd.confにsnmpdのデフォルト設定ファイルが置かれるので、監視サーバから情報を取 得できるように編集する。

まず、監視サーバからの読み出し許可設定を追記。SNMPコミュニティ名は「public」がデフォルト設定としては一般的だが、適宜設定。

[text]
  
\# 監視対象で設定
  
rocommunity SNMPコミュニティ名 監視サーバのIP # 追記
  
view all all # 追記
  
[/text]

全インタフェースからの接続を許可するよう設定。

[text]
  
\# 監視対象で設定
  
#agentAddress udp:127.0.0.1:161 # この行はコメントアウト
  
agentAddress udp:161 # 追記
  
[/text]

snmpdを再起動した後、監視サーバから以下のコマンドを実行して応答があればSNMPの設定は完了。

[bash]
  
target$ sudo /etc/init.d/snmpd restart
  
[/bash]
  
[bash]
  
server$ snmpwalk -v1 -c public 監視対象IPアドレス .1.3.6.1.2.1.1.1.0
  
SNMPv2-MIB::sysDescr.0 = STRING: Linux ubuntu1110s-01 2.6.38-8-server #42-Ubuntu SMP Mon Apr 11 03:49:04 UTC 2011 x86_64
  
↑ 応答の例
  
[/bash]

## SNMPの設定(CentOSの場合)

通常、SNMPはデフォルトでインストールされるので、設定ファイルを編集して監視サーバから接続できるようにする。基本的にはUbuntuと同じ設定にすればよい。

[bash]
  
\# 監視対象で設定
  
rocommunity SNMPコミュニティ名 監視サーバのIP # 追記
  
view all all # 追記
  
[/bash]

snmpdが自動起動するように設定し(どうやら初期設定では自動起動しないようになっている模様)、snmpdを再起動して設定を反映させる。

[bash]
  
target$ sudo chkconfig snmpd on
  
target$ sudo /etc/init.d/snmpd start
  
[/bash]
  
[bash]
  
server$ snmpwalk -v1 -c public 監視対象IPアドレス .1.3.6.1.2.1.1.1.0
  
SNMPv2-MIB::sysDescr.0 = STRING: Linux centos62-64-01 2.6.32-220.el6.x86\_64 #1 SMP Tue Dec 6 19:48:22 GMT 2011 x86\_64
  
↑ 応答の例
  
[/bash]

## Cacti監視のためのMySQL設定

Percona Monitoring Plugins for Cactiは、監視対象のMySQLにログインして、色々な種類のshowコ マンドを実行して得られた結果をグラフにするので、監視対象のMySQLにユーザを追加する必要があ る。ここで指定するユーザ名とパスワードは、<a title="MySQLの監視はCacti+Percona Monitoring Pluginsがおすすめ(監視サーバ構築編)" href="http://b.l0g.jp/mysql/install-cacti-to-monitor-mysql-1/" target="_blank">前回のエントリ</a>でss\_get\_mysql\_stats.phpファイルに書いた、 $mysql\_user と $mysql_pass と一致させること。

[bash]
  
target$ mysql -uroot
  
mysql-target> grant process, super on \*.\* to ユーザ名@監視サーバ identified by &#8216;パスワ ード&#8217;;
  
mysql-target> flush privileges;
  
[/bash]

## Cactiからの監視設定

監視サーバのCacti管理画面で、画像のように Create → New Graphs → Create New Host とたどり、監視対象デバイス作成画面を開く。

<a href="http://b.l0g.jp/mysql/use-cacti-to-monitor-mysql-2/attachment/05/" rel="attachment wp-att-1215"><img src="http://b.l0g.jp/wp-content/uploads/2012/05/05.png" title="デバイ ス作成画面" width="586" height="372" class="alignnone size-full wp-image-1215" style="border: 0px;" /></a>

以下のように入力し、「Create」ボタンを押す。

<table border="" align="center">
  <tr>
    <td>
      Description
    </td>
    
    <td>
      監視対象ホスト名など、一意に監視対象がわかる記述
    </td>
  </tr>
  
  <tr>
    <td>
      Hostname
    </td>
    
    <td>
      監視対象IPアドレスあるいは名前解決できる場合ホスト名
    </td>
  </tr>
  
  <tr>
    <td>
      Host Template
    </td>
    
    <td>
      Percona MySQL Server HT
    </td>
  </tr>
  
  <tr>
    <td>
      SNMP Community, SNMP Port
    </td>
    
    <td>
      SNMPコミュニティ名にpublic以外を指定した場合は適宜設定
    </td>
  </tr>
</table>

Createボタンを押した後、画面下側に関連づけられたグラフが一覧表示される。Percona Monitoring Pluginsで取得できる情報は、あくまでMySQLのものだけで、実際にはLinuxの情報も取得しておく必要があるので、Host Templateに「Linux Server Default Set」を選んでもう一度Createボタンを押 して、最低限のOS関連情報も取れるようにしておこう。

ここまで時点では、画面下側に表示されたグラフテンプレートがホストにひもづけられただけで、実際にグラフの作成は開始されていない。右上の「Create New Graphs」をクリックし、画像のように 作成したいグラフを選択して、「Create」を押すと、対応するグラフの情報取得が始まる。

<a href="http://b.l0g.jp/mysql/use-cacti-to-monitor-mysql-2/attachment/06/" rel="attachment wp-att-1237"><img src="http://b.l0g.jp/wp-content/uploads/2012/05/06.png" title="グラフ 作成" width="592" height="287" class="alignnone size-full wp-image-1237" style="border: 0px;" /></a>

<a href="http://b.l0g.jp/mysql/use-cacti-to-monitor-mysql-2/attachment/07/" rel="attachment wp-att-1239"><img src="http://b.l0g.jp/wp-content/uploads/2012/05/07.png" title="グラフ 作成" width="592" height="470" class="alignnone size-full wp-image-1239" style="border: 0px;" /></a>

## ツリーへのホスト追加

この状態では左上の「graphs」タブに移動しても左側のツリーには追加したサーバは表示されていないだろう。もう一度「console」タブに戻り、 Management → Devices から、監視対象デバイスの一覧を開き、今追加したサーバにチェックを付ける。右下の「Choose an action」から、「Add graph to tree」を選択してGoボタンを押し、ツリーに追加すると、めでたく graph タブのツリーにホストが現れ、グラフが表示される。

<a href="http://b.l0g.jp/mysql/use-cacti-to-monitor-mysql-2/attachment/08/" rel="attachment wp-att-1240"><img src="http://b.l0g.jp/wp-content/uploads/2012/05/08.png" title="ツリー に追加" width="592" height="353" class="alignnone size-full wp-image-1240" style="border: 0px;" /></a>

画像がリンク切れの場合、まだグラフを描画できる情報が集まっていないので、少し(5分おきの情報収集がデフォルトなので、2回分10分以上)待ってもう一度確認しよう。画像は表示されたのに、いくら待ってもグラフが表示されない場合は、監視対象から情報が取得できていない可能性があるので、SNMPの設定やMySQLの設定(特にユーザ関連)を見直すべし。

グラフをどう読み取っていくかについては、以下の記事が参考になる。記事中には「better-cacti-template」とあるが、これはPercona Monitoring Plugin for Cactiの前身となるものなので、ほぼ同じと考えてよい。

<a title="これだけ見れば大丈夫！ーMySQLパフォーマンス監視のツボ（クエリ編）" href="http://www.infiniteloop.co.jp/blog/2012/03/mysql-tuning-cacti-query/" target="_blank">これだけ見 れば大丈夫！ーMySQLパフォーマンス監視のツボ（クエリ編）</a>

今回はPercona Monitoring Plugin for CactiのMySQLに関する部分だけを紹介したが、ApacheやMemcached、nginx、redisなどの情報グラフ化もできるので、お試しあれ。



* * *

**海外の役立つブログ記事などを人力で翻訳して公開する[Yakst](https://yakst.com/ja)というプロジェクトをやっています。よろしければそちらもどうぞ！**
