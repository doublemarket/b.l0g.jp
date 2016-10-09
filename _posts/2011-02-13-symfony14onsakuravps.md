---
id: 637
title: さくらVPSのCentOSにsymfony 1.4.xの環境を構築する
date: 2011-02-13T22:25:12+00:00
author: doublemarket
layout: post
guid: http://b.l0g.jp/?p=637
permalink: /dev/symfony14onsakuravps/
categories:
  - 開発
---

自分でWebサービスを作ってみようと思い立ってsymfonyを勉強し始めて数ヶ月。
  
しばらく前の話だが、借りていたサーバの使用期限が終わってしまったので、さくらVPSを契約。そこにsymfony 1.4.x(1.4.8)をインストールするときの手順をまとめておく。特にさくらVPSに特化した内容はないので、普通にCentOS上にsymfony 1.4をインストールする手順と同じのはず。

## ＜事前準備・参考＞

以下のサイトを参考にして、不要なパッケージの削除、サービス停止、ファイアウォールやSSHなど、基本的なインターネット公開サーバの設定を済ませておく。

 <a href="http://tanaka.sakura.ad.jp/archives/001065.html" target="_blank">http://tanaka.sakura.ad.jp/archives/001065.html</a>
  
 <a href="http://www.ideaxidea.com/archives/2010/11/sakura_vps_settings.html" target="_blank">http://www.ideaxidea.com/archives/2010/11/sakura_vps_settings.html</a>

## ＜PHP5.3を配布しているremiリポジトリを有効にする＞

このブログ執筆時点のさくらVPSのデフォルトOSはCentOS 5.5(x86_64)だが、これには
  
PHP5.1.6が含まれており、symfony 1.4.xが動作しないので、最新版5.3をインストール。

PHP5.3のRPMパッケージはremiというリポジトリから配布されているので、yumのリポジトリに設定を追加。

```
  
\# rpm -Uvh http://download.fedora.redhat.com/pub/epel/5/x86_64/epel-release-5-4.noarch.rpm
  
\# rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-5.rpm
  
```

## ＜PHP5.3インストール＞

```
  
\# yum install -enablerepo=remi php php-domxml php-devel php-mysql
  
```

php.iniに以下を追記。追記しないとdate()の使用で警告が出る。

```
  
date.timezone = "Asia/Tokyo"
  
```

mb_string関連の関数を使用するために必要なのでphp-mbstringもインストール。

```
  
\# yum install -enablerepo=remi php-mbstring
  
```

コンソールで文字を色付けするのに必要なのでphp-processもインストール。
  
```
  
\# yum install -enablerepo=remi php-process
  
```

symfonyで必要なものがそろっているか確認するスクリプトを実行。

```
  
\# wget http://sf-to.org/1.4/check.php
  
\# php check_configuration.php
  
\***\***\***\***\***\***\***\***\***\*****
  
\* \*
  
\* symfony requirements check \*
  
\* \*
  
\***\***\***\***\***\***\***\***\***\*****

php.ini used by PHP: /etc/php.ini

\*\* WARNING \*\*
  
* The PHP CLI can use a different php.ini file
  
* than the one used with your web server.
  
* If this is the case, please launch this
  
* utility from your web server.
  
\*\* WARNING \*\*

\*\* Mandatory requirements \*\*

OK PHP version is at least 5.2.4 (5.3.3)

\*\* Optional checks \*\*

OK PDO is installed
  
OK PDO has some drivers installed: mysql, sqlite, sqlite2
  
OK PHP-XML module is installed
  
OK XSL module is installed
  
OK The token\_get\_all() function is available
  
OK The mb_strlen() function is available
  
OK The iconv() function is available
  
OK The utf8_decode() is available
  
OK The posix_isatty() is available
  
[[WARNING]] A PHP accelerator is installed: FAILED
  
\*\\*\* Install a PHP accelerator like APC (highly recommended) \*\**
  
OK php.ini has short\_open\_tag set to off
  
OK php.ini has magic\_quotes\_gpc set to off
  
OK php.ini has register_globals set to off
  
OK php.ini has session.auto_start set to off
  
OK PHP version is not 5.2.9
  
```

## ＜pearインストール＞

※ pearでのインストールは推奨されていないが、簡単なのでこれで済ませる
  
```
  
\# yum install -enablerepo=remi php-pear
  
\# pear upgrade-all
  
```

## ＜symfonyインストール＞

```
  
\# pear channel-discover pear.symfony-project.com
  
\# pear install symfony/symfony
  
```

以下のコマンドでバージョンが表示されたらインストール成功。

```
  
\# symfony -V
  
symfony version 1.4.8 (/usr/share/pear/symfony)
  
```

/usr/share/pear/symfonyにインストールされる

## ＜APCインストール＞

PHPのアクセラレータであるAPCをインストールしておく。入れなくても動作するが、symfonyでは導入が推奨されているのと、入れただけで高速になるとのことなのでインストール。

```
  
\# yum install httpd-devel
  
\# yum install -enablerepo=remi php-pecl-apc
  
```

## ＜httpd起動＞

```
  
\# chkconfig httpd on
  
Syntax OK
  
\# service httpd start
  
```

ブラウザから http://(サーバのIPアドレス)/ を確認し、phpinfoでapcが読み込まれていることを確認。

※ phpinfoはコマンドラインから以下を実行しても確認できる

```
  
php 'phpinfo();'
  
```

## ＜MySQLインストール＞

```
  
\# yum install -enablerepo=remi mysql-server
  
```

## ＜MySQL設定＞

my.cnfに以下を追記して文字コードをUTF-8に
  
```
  
[mysqld]
  
default-character-set=utf8
  
character\_set\_server=utf8
  
skip-character-set-client-handshake
  
[mysql]
  
default-character-set=utf8
  
```

statusコマンドおよび"show variables like ‘char%’;"でUTF-8になっていることを確認(<a href="http://b.l0g.jp/dev/symfony%E3%81%A7%E3%83%87%E3%83%BC%E3%82%BF%E3%82%92mysql%E3%81%AB%E3%83%AD%E3%83%BC%E3%83%89%E3%81%99%E3%82%8B%E9%9A%9B%E3%81%AEduplicate-entry%E3%82%A8%E3%83%A9%E3%83%BC/" target="_blank">過去エントリ</a>も参照)

パスワードを設定
  
```
  
\# mysqladmin -u root password パスワード
  
```
  
データベース作成
  
```
  
\# mysql -uroot -p
  
mysql> create database データベース名;
  
```

## ＜postfixインストール＞

```
  
\# yum remove sendmail
  
\# yum install postfix
  
```

/etc/postfix/main.cf を編集
  
```
  
\# chkconfig postfix on
  
\# service postfix start
  
```

メール送信のテスト
  
```
  
\# mail -s test あて先
  
```
  
※ 本登録前のお試し期間中はOP25Bが有効なので、VPS上のサーバからメールを送信することはできない

