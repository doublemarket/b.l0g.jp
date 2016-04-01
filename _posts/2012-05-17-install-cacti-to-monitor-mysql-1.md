---
id: 1084
title: MySQLの監視はCacti+Percona Monitoring Pluginsがおすすめ(監視サーバ構築編)
date: 2012-05-17T20:01:24+00:00
author: doublemarket
layout: post
guid: http://b.l0g.jp/?p=1084
permalink: /mysql/install-cacti-to-monitor-mysql-1/
categories:
  - Cacti
  - MySQL
---

MySQLをリソース監視する仕組みにはいくつかあるが、対象のMySQLサーバが5台以上ある場合はCactiがおすすめ。導入のしやすさだけでMuninを選ぶ人が多い気がするが、その選択基準は間違っている！

<a href="http://b.l0g.jp/mysql/install-cacti-to-monitor-mysql-1/attachment/cacti-211916/" rel="attachment wp-att-1173"><img src="http://b.l0g.jp/wp-content/uploads/2012/05/Cacti-211916.png" title="Cactiグラフの例" width="223" height="256" class="alignleft  wp-image-1173" style="margin-top: 0px; margin-bottom: 0px; margin-left: 10px; margin-right: 40px; border-style: initial; border-color: initial; border-image: initial; border-width: 0px;" /></a>

&nbsp;

**Cactiのいいとこわるいとこ**

  * 多数のグラフを見やすく並べられる
  * muninと比べて多数のサーバから軽快に情報を収集・表示できる
  * 監視対象には、MySQLのユーザを追加するだけでかなりの項目数を監視できる
  * データの保存にデータベースが必須だったりしてセットアップがやや面倒
  * 慣れるまで監視プラグインを書くのに手間取る

**Muninのいいとこわるいとこ**

  * 監視プラグインを書くのが簡単
  * 監視サーバにデータベースなどが必要なく、セットアップが簡単
  * グラフの並び方などが固定で、比較などがしにくい
  * 監視対象にプラグインを入れなければならず、台数が多いとそれだけ手間が増える
  * 監視対象が増えると監視サーバの負荷が急激にあがるので、あまり多数のサーバは監視できない

そんなわけで、CentOS 6.2上にCactiの監視サーバをセットアップし、<a title="Percona Monitoring Plugin" href="http://www.percona.com/software/percona-monitoring-plugins/" target="_blank">Percona Monitoring Plugins for Cacti</a>を使ってMySQLサーバ(CentOSまたはUbuntu)のリソース監視を始めるまでの手順。その1は監視サーバのセットアップ編。

## RPMforgeのリポジトリ追加

RPMforgeリポジトリには最新のCactiが含まれているため、これを導入する。

```
  
$ wget http://pkgs.repoforge.org/rpmforge-release/rpmforge-release-0.5.2-2.el6.rf.x86_64.rpm
  
$ sudo rpm -ihv rpmforge-release-0.5.2-2.el6.rf.x86_64.rpm
  
$ sudo yum -y update rpmforge-release
  
```

/etc/yum.repos.d/rpmforge.repo ファイルをエディタで開き、 enabled = 0 にする。これで、オプションを付けないとRPMforgeはリポジトリとして有効にならないようになる。

## Cactiのインストール

Cactiと、あわせて必要なnet-snmpやhttpd、MySQLなどをインストール。

```
  
$ sudo yum install -enablerepo=rpmforge cacti net-snmp-utils mysql-server
  
```

指定したもの以外に、依存関係上httpd, mysql, perl, php, rrdtoolなどもインストールされる。

## MySQLの設定

Cactiが取得した情報を格納するためのMySQLを設定する。ここでは最低限必要な設定だけを記述するが、CentOS 6.2のMySQLの/etc/my.cnfは非常にシンプルなので、必要に応じてパラメータを指定すること。

[mysqld]セクションに以下を追加

```
  
skip-character-set-client-handshake
  
character-set-server = utf8
  
collation-server = utf8\_general\_ci
  
init-connect = SET NAMES utf8
  
```

(2012.05.29) default-character-setは不要とnippondanjiさんからはてブコメントで指摘をいただいたので削除。直々にご指摘とは恐縮です。

ここでMySQLを再起動する。

```
  
$ sudo /etc/init.d/mysqld restart
  
```

statusコマンドで、charactersetがutf8であることを確認。

```
  
mysql> status;
  
(略)
  
Server characterset: utf8
  
Db characterset: utf8
  
Client characterset: utf8
  
Conn. characterset: utf8
  
(略)
  
```

<a href="http://docs.cacti.net/manual:088:1_installation.1_install_unix.5_install_and_configure_cacti" target="_blank">Cactiのドキュメント</a>にしたがって、Cactiで使用するMySQLの設定を行う。

```
  
$ mysqladmin -user=root create cacti
  
$ mysql cacti < cacti.sql
  
$ mysql -user=root mysql
  
mysql> grant all on cacti.* to ユーザ名@localhost identified by 'パスワード';
  
mysql> flush privileges;
  
```

## Apache/PHPの設定

/var/www/cacti/include/config.php を上の項で作成したユーザに合わせて編集する。

```
  
$database_type = "mysql";
  
$database_default = "cacti";
  
$database_hostname = "localhost"; # DBサーバのホスト名
  
$database_username = "cactiuser"; # 上の項で作成したユーザ
  
$database_password = "cactipassword"; # 上の項で設定したパスワード
  
```

/etc/httpd/conf.d/cacti.php が存在することを確認する。他のホストからCactiにアクセスする際は、以下の行を追加してhttpdを再起動する。

```
  
allow from 127.0.0.1
  
allow from IPアドレス # 追記
  
```

<a href="http://docs.cacti.net/manual:088:1_installation.1_install_unix.1_configure_php" target="_blank">Cactiのドキュメント</a>を参考に、/etc/php.ini および /etc/php.d/*.ini の記述を確認。設定を変更したら、Apacheを再起動する。

```
  
$ sudo /etc/init.d/httpd restart
  
```

## iptablesの設定

CentOS 6.2ではデフォルトでpingやSSHしか応答できないようにされているようなので、/etc/sysconfig/iptables に以下を追加してhttpのアクセスが可能なようにする。

```
  
-A INPUT -m state -state NEW -m tcp -p tcp -dport 80 -j ACCEPT
  
```

iptablesを再起動

```
  
$ sudo /etc/init.d/iptables restart
  
```

## 設定完了

Cactiをインストールした際に /etc/cron.d/cacti が既に作成されているので、この時点で5分おきに情報収集が始まっている。

http://CactiサーバのIPアドレス/cacti を開くと、まずインストールの確認ウィザードが開始される。

<p style="text-align: center;">
  <a href="http://b.l0g.jp/mysql/install-cacti-to-monitor-mysql-1/attachment/03-2/" rel="attachment wp-att-1159"><img src="http://b.l0g.jp/wp-content/uploads/2012/05/03.png" title="インストール確認ウィザード" width="305" height="244" class="size-full wp-image-1159 aligncenter" style="border-style: initial; border-color: initial; border-image: initial; border-width: 0px;" /></a>
</p>

ここまでの設定を改めて確認されるだけなので、基本的にはNextを押していけばいい。各種ツールのパスを確認される画面が最後にあるが、ここでパスが見つからない場合は手動でパスを入力するか、不足しているツールをインストールして再度試すべし。

この後ログイン画面が表示され、ユーザ名 admin 、 パスワード admin でログイン可能で、すぐにパスワードの変更を促されるので画面の指示に従って変更する。

## Percona Monitoring Plugin for Cactiの導入

<a href="http://www.percona.com/downloads/percona-monitoring-plugins/" target="_blank">Percona</a>からPercona monitoring pluginsをダウンロードし、ファイルを展開する。

```
  
$ tar zxvf percona-monitoring-plugins-1.0.0.tar.gz
  
$ cd percona-monitoring-plugins-1.0.0/cacti/scripts
  
$ sudo cp ss\_get\_by\_ssh.php ss\_get\_mysql\_stats.php /var/www/cacti/scripts/
  
```

監視対象のMySQLにログインする際のアカウント情報を ss\_get\_mysql_stats.php に書き込む。

```
  
$mysql_user = 'cactiuser'; # 監視対象にログインする際のMySQLユーザ
  
$mysql_pass = 'cactiuser'; # 監視対象にログインする際のパスワード
  
```

Cacti管理画面からImport/Export → Import templatesを選択し、Import Template from Local Fileで以下を指定してImportボタンを押し、テンプレートファイルを読み込ませる。

```
  
percona-monitoring-plugins-1.0.0/cacti/templates/cacti\_host\_template\_percona\_mysql\_server\_ht_0.8.6i-sver1.0.0.xml
  
```

成功すると、それぞれの監視項目に対応したテンプレートごとに[success]と表示される。

<p style="text-align: center;">
  <a href="http://b.l0g.jp/mysql/install-cacti-to-monitor-mysql-1/attachment/04-2/" rel="attachment wp-att-1160"><img src="http://b.l0g.jp/wp-content/uploads/2012/05/04.png" title="テンプレート読み込み" width="360" height="393" class="size-full wp-image-1160 aligncenter" style="border-style: initial; border-color: initial; border-image: initial; border-width: 0px;" /></a>
</p>

これでMySQLサーバを監視するための監視サーバの準備は完了。

(2012.05.28追記) 監視対象側の設定についても書いたので、続けてどうぞ。

[MySQLの監視はCacti+Percona Monitoring Pluginsがおすすめ(監視対象設定編)](http://b.l0g.jp/mysql/use-cacti-to-monitor-mysql-2/ "MySQLの監視はCacti+Percona Monitoring Pluginsがおすすめ(監視対象設定編)")

* * *

**海外の役立つブログ記事などを人力で翻訳して公開する[Yakst](https://yakst.com/ja)というプロジェクトをやっています。よろしければそちらもどうぞ！**
