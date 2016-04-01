---
id: 1482
title: Nagiosの設定ファイル生成ツールnconfが使いやすいのであーる
date: 2013-06-09T15:49:25+00:00
author: doublemarket
layout: post
guid: http://b.l0g.jp/?p=1482
permalink: /nagios/setup-nconf/
categories:
  - Nagios
---

Nagiosの設定ファイルを編集するツールには、<a title="NagiosQL" href="http://www.nagiosql.org/" target="_blank">NagiosQL</a>など<a title="Nagios Config Tools" href="http://www.nagios.org/projects/nagiosconfigtools" target="_blank">いくつかある</a>が、あれこれ触ってみた感じ<a title="nconf" href="http://www.nconf.org/dokuwiki/doku.php" target="_blank">nconf</a>がよさげだった。JQueryなどを適宜使って動きは軽快だし、メニューの構成などもストレートで直感的に使えるところがNagiosQLよりも気にいった。

監視の設定はできる限り自動生成するくらいが大規模なシステムでは最近の主流かもしれないが、そこまでの大きさのシステムではない時でも、設定ファイルを手で編集するのはいただけない。Nagiosを使うならとりあえずnconfも一緒に入れて設定すべし。

最新版が2011年で開発が活発でないのは気になるが、基本的な機能は網羅されており不自由は感じないので、しばらく使っている。ってことで、CentOS6.4にNagiosとnconfをインストールして、監視を始めるまでの手順。

## Nagiosのインストール

epelリポジトリにNagiosが含まれているので、epelをインストールしてからNagiosと、監視に使うプラグインをインストール。

```
  
$ sudo yum install http://ftp-srv2.kddilabs.jp/Linux/distributions/fedora/epel/6/x86_64/epel-release-6-8.noarch.rpm
  
$ sudo yum install nagios nagios-plugins
  
```

Nagiosと一緒に、PHPやPerl、Apacheなども一緒にインストールされる。

```
  
$ sudo /etc/init.d/nagios start
  
$ sudo /etc/init.d/httpd start
  
```

NagiosとApacheを起動したら、 http://(サーバのIP)/nagios をブラウザで開いて、ユーザ名 nagiosadmin、パスワード nagiosadmin でログインするとNagios管理画面が開ければNagiosの準備は一旦終わり。

<a href="http://b.l0g.jp/wp-content/uploads/2013/06/Nagios-Core.png" target="_blank"><img class="alignnone  wp-image-1484" style="border: 0px;" alt="Nagios Core" src="http://b.l0g.jp/wp-content/uploads/2013/06/Nagios-Core.png" width="408" height="245" /></a>

## MySQLのインストール

nconfの特徴として、設定をMySQLに入れておいて、そこからNagiosの設定ファイルを生成することが挙げられる。そのためにMySQLをインストール。nconfはPHPで書かれており、PHPからMySQLへ接続するためのphp-mysqlも一緒にインストール。

```
  
$ sudo yum install mysql-server php-mysql
  
```

mysql, mysql-libsなどもインストールされる。

[この辺](http://b.l0g.jp/mysql/install-cacti-to-monitor-mysql-1/)参照して、/etc/my.cnf の [mysqld] セクションに最低限の設定をしてから、MySQLを起動。

```
  
$ sudo /etc/init.d/mysqld start
  
```

nconf用データベースと、MySQLへ接続するためのアカウントも作成。以下の例では、データベース名がnconf、ユーザ名がnconf、パスワードはnconfpasswordとしているが、任意のものにできるので適宜変更すべし。

```
  
$ mysql -uroot
  
mysql> create database nconf;
  
Query OK, 1 row affected (0.00 sec)

mysql> grant SELECT, INSERT, UPDATE, DELETE, CREATE, ALTER, DROP on nconf.* to 'nconf'@'localhost' identified by 'nconfpassword';
  
Query OK, 0 rows affected (0.00 sec)
  
```

## nconfのインストール

[nconfのサイト](http://www.nconf.org/)のDownloadからnconf-1.3.0-0.tgzをダウンロードして適当なディレクトリに配置。CentOSのApacheのDocumentRootに展開する。

```
  
cd /var/www/html
  
sudo tar zxvf /fuga/hoge/nconf-1.3.0-0.tgz
  
chown -R apache. nconf
  
```

インストールに必要な設定は、GUIで行える。http://(サーバのIP)/nconf/ をブラウザで開く。

<a href="http://b.l0g.jp/wp-content/uploads/2013/06/nconf01.png" target="_blank"><img class="alignnone  wp-image-1488" style="border: 0px;" alt="nconf01" src="http://b.l0g.jp/wp-content/uploads/2013/06/nconf01.png" width="665" height="430" /></a>

ここで画面がforbiddenになる場合、SELinuxが有効になっている可能性がある(/var/log/audit/audit.logにその旨出力されているはずなので確認すべし)。そんな時は頑張って動くように設定するか、そっと/etc/sysconfig/selinuxのSELINUX=enabledをdisabledに書き換えてOSを再起動する。

必須のパッケージが全てインストールされていれば、最初の画面でOKが並ぶので、Nextをクリック。

<a href="http://b.l0g.jp/wp-content/uploads/2013/06/nconf02.png" target="_blank"><img class="alignnone  wp-image-1489" style="border: 0px;" alt="nconf02" src="http://b.l0g.jp/wp-content/uploads/2013/06/nconf02.png" width="665" height="430" /></a>

前項でMySQLに作成したデータベース名とアカウントを入力してNextを押すとチェックが走るので、OKが並んだらNext。

<a href="http://b.l0g.jp/wp-content/uploads/2013/06/nconf03.png" target="_blank"><img class="alignnone  wp-image-1490" style="border: 0px;" alt="nconf03" src="http://b.l0g.jp/wp-content/uploads/2013/06/nconf03.png" width="665" height="430" /></a>

nconfの接続に認証を必要とするかどうかの設定。後でも変更できるので一旦そのまま(AUTH_ENABLEDがFALSE)でNext。

<a href="http://b.l0g.jp/wp-content/uploads/2013/06/nconf04.png" target="_blank"><img class="alignnone  wp-image-1491" style="border: 0px;" alt="nconf04" src="http://b.l0g.jp/wp-content/uploads/2013/06/nconf04.png" width="665" height="430" /></a>

全て完了したらFinishをクリック。

<a href="http://b.l0g.jp/wp-content/uploads/2013/06/nconf05.png" target="_blank"><img class="alignnone  wp-image-1492" style="border: 0px;" alt="nconf05" src="http://b.l0g.jp/wp-content/uploads/2013/06/nconf05.png" width="665" height="430" /></a>

インストールしたディレクトリ内のファイル、ディレクトリを削除しろというメッセージが出るので、リネームする(別に使わないと思うが一応)。

```
  
$ cd /var/www/html/nconf
  
$ sudo mv INSTALL INSTALL.org
  
$ sudo mv INSTALL.php INSTALL.php.org
  
$ sudo mv UPDATE UPDATE.org
  
$ sudo mv UPDATE.php UPDATE.php.org
  
```

ここまででnconfの設定画面にはアクセスできるようになる。

<a href="http://b.l0g.jp/wp-content/uploads/2013/06/nconf07.png" target="_blank"><img class="alignnone  wp-image-1504" style="border: 0px;" alt="nconf07" src="http://b.l0g.jp/wp-content/uploads/2013/06/nconf07.png" width="665" height="430" /></a>

## nconfの設定・Nagiosの追加設定

前述の通りnconfはMySQLに設定を書いていて、それをNagiosの設定ファイルとして書き出し、Nagiosを再起動して設定を反映させるところまでnconfでやることができる。そのための追加設定がここから。

/var/www/html/nconf/config/nconf.php (nconfのメイン設定ファイル)を以下の通り書き換え。

23行目。Nagios本体のパスを指定する。

```
  
\# 変更前
  
define('NAGIOS_BIN', '/var/www/html/nconf/bin/nagios');
  
\# 変更後
  
define('NAGIOS_BIN', '/usr/sbin/nagios');
  
```

75行目。Nagiosのメイン設定ファイル(nagios.cfg)の構文チェックを無効化。手元の環境では、有効にしてしまうと、正しいはずの設定ファイルのチェックがどうしてもエラーになってしまったのでやむなく。

```
  
\# 変更前
  
define('CHECK\_STATIC\_SYNTAX', 1);
  
\# 変更後
  
define('CHECK\_STATIC\_SYNTAX', 0);
  
```

80行目。この配列に入っているユーザが、通知先などに自動で追加されてしまうので削除。

```
  
\# 変更前
  
$SUPERADMIN_GROUPS = array ("+admins");
  
\# 変更後
  
$SUPERADMIN_GROUPS = array ("");
  
```

/var/www/html/nconf/config/deployment.ini (nconfの自動デプロイ設定ファイル)を以下の通り書き換え。

「;; LOCAL deployment ;;」の下、6行目から29行目までの以下のセクションの行頭のセミコロンを外して有効化。

```
  
;[extract config]
  
;[copy collector config]
  
;[copy global config]
  
;[copy nagios.cfg]
  
```

また、nconfが生成するNagios設定ファイルのアーカイブを指定する、8行目を以下のとおり変更。

```
  
\# 変更前
  
source_file = "/var/www/nconf/output/NagiosConfig.tgz"
  
\# 変更後
  
source_file = "/var/www/html/nconf/output/NagiosConfig.tgz"
  
```

Apacheの起動ユーザapacheがNagiosを再起動できるようにsudoersを編集。

```
  
$ sudo visudo
  
```

以下の1行を末尾に追加。

```
  
apache ALL=(ALL)NOPASSWD: /etc/rc.d/init.d/nagios reload
  
```

nconfが生成した設定ファイルをNagiosの設定ファイルディレクトリ(/etc/nagios)に配置できるように細々した設定。

```
  
$ cd /etc/nagios
  
$ sudo mkdir Default_collector global
  
$ sudo chown apache. Default_collector global nagios.cfg
  
```

nconfからnagios.cfgを編集・配置することもできるので、一応nconfのディレクトリ以下にnagios.cfgを置いておく。

```
  
$ sudo cp /etc/nagios/nagios.cfg /var/www/html/nconf/static_cfg/
  
```

nconfが配置するファイルを設定ファイルとして使用するように、コピーした先の/var/www/html/nconf/static_cfg/nagios.cfgを編集。

```
  
\# cfg\_fileとcfg\_dirを全てコメントアウトして以下の2行を追加。

cfg_dir=/etc/nagios/global
  
cfg\_dir=/etc/nagios/Default\_collector
  
```

これで設定はおしまい。ApacheとNagiosを再起動して一息つく。

```
  
$ sudo /etc/init.d/httpd stop
  
$ sudo /etc/init.d/nagios restart
  
$ sudo /etc/init.d/httpd start
  
```

## 動作確認

http://(サーバのIP)/nagios/ を開いて、左側のCurrent Status → Servicesをクリックすると、Nagios側では以下のようにデフォルトでlocalhostの監視のみが設定されている。

<a href="http://b.l0g.jp/wp-content/uploads/2013/06/nagios02.png" target="_blank"><img class="alignnone  wp-image-1493" style="border: 0px;" alt="nagios02" src="http://b.l0g.jp/wp-content/uploads/2013/06/nagios02.png" width="680" height="409" /></a>

一方、 http://(サーバのIP)/nconf/ を開いて、左側のBasic Items → Hostsをクリックすると、nconf側ではlocalhost以外にサンプルとしてhplj2605dnなど合わせて4台分の設定が入っていることが分かる。

<a href="http://b.l0g.jp/wp-content/uploads/2013/06/nconf08.png" target="_blank"><img class="alignnone  wp-image-1494" style="border: 0px;" alt="nconf08" src="http://b.l0g.jp/wp-content/uploads/2013/06/nconf08.png" width="665" height="430" /></a>

ここで、nconfの左側のメニューからBasic Items → Generate Nagios configをクリックすると、設定ファイルが生成される。以下の画面で「Deploy」をクリックする。

<a href="http://b.l0g.jp/wp-content/uploads/2013/06/nconf09.png" target="_blank"><img class="alignnone  wp-image-1495" style="border: 0px;" alt="nconf09" src="http://b.l0g.jp/wp-content/uploads/2013/06/nconf09.png" width="665" height="430" /></a>

以下の画面のように、全てOKになれば、設定ファイルのデプロイとNagiosの再起動が終わっているはず。

<a href="http://b.l0g.jp/wp-content/uploads/2013/06/nconf10.png" target="_blank"><img class="alignnone  wp-image-1496" style="border: 0px;" alt="nconf10" src="http://b.l0g.jp/wp-content/uploads/2013/06/nconf10.png" width="665" height="430" /></a>

ここで、Nagiosの画面を開くと、以下のようにnconfに入っていた設定が有効になって、ホストが増えていれば成功。

<a href="http://b.l0g.jp/wp-content/uploads/2013/06/nagios03.png" target="_blank"><img class="alignnone  wp-image-1506" style="border: 0px;" alt="nagios03" src="http://b.l0g.jp/wp-content/uploads/2013/06/nagios03.png" width="680" height="380" /></a>

後は、nconfからあれこれ設定を入れて監視を始めるべし。



* * *

**海外の役立つブログ記事などを人力で翻訳して公開する[Yakst](https://yakst.com/ja)というプロジェクトをやっています。よろしければそちらもどうぞ！**
