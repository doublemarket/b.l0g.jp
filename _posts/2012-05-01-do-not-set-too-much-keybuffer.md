---
id: 1003
title: MyISAMを使っている時key_buffer_sizeは大きくし過ぎてもいけない
date: 2012-05-01T20:46:40+00:00
author: doublemarket
layout: post
guid: http://b.l0g.jp/?p=1003
permalink: /mysql/do-not-set-too-much-keybuffer/
categories:
  - MySQL
---

MyISAMのみを使っているMySQLサーバで、key\_buffer\_sizeのサイズは大きくても小さくてもダメですよ、という例。

その前にちょっと復習。MySQLの主なストレージエンジンといえばMyISAMとInnoDBだが、データやインデックスのキャッシュの仕組みには、

  * InnoDB : インデックス、データともMySQLがキャッシュ管理する(innodb\_buffer\_pool_sizeで設定)
  * MyISAM : インデックスはMySQLがキャッシュ管理する(key\_buffer\_sizeで大きさを設定)。データはOSのキャッシュ機構におまかせ

という違いがある。

ものすごく簡単に言えば、InnoDBの場合はなるべく大きな innodb\_buffer\_pool\_size を設定してやれば、インデックスかデータかに関わらずメモリ上にキャッシュされて高速化が図れる可能性がある。一方MyISAMの場合、key\_buffer_size を大きくしてもインデックスしかキャッシュされないので、OSがデータ部分をキャッシュするメモリ(つまりMySQLに割り当ててないメモリ)もある程度確保しておく必要がある。この辺りのことはMySQLのドキュメントには以下のように書かれている。

> innodb\_buffer\_pool_size
  
> 専用サーバの場合物理メモリの80%。
  
> <a title="MySQL 5.1 リファレンスマニュアル 13.5.4. InnoDB 起動オプションとシステム変数" href="http://dev.mysql.com/doc/refman/5.1/ja/innodb-parameters.html" target="_blank">MySQL 5.1 リファレンスマニュアル 13.5.4. InnoDB 起動オプションとシステム変数</a>

> key\_buffer\_size
  
> 「一般的には、マシンのメモリ使用率 25 % の値であることが好ましい」一方で
  
> 「Key\_reads/Key\_read_requests の比率は、0.01 より小さいことが望ましい」つまり
  
> 99.9%がキャッシュされていることが望ましいということ。
  
> <a title="MySQL 5.1 リファレンスマニュアル 4.2.3. システム変数" href="http://dev.mysql.com/doc/refman/5.1/ja/server-system-variables.html" target="_blank">MySQL 5.1 リファレンスマニュアル 4.2.3. システム変数</a>

とある参照用のスレーブ(MyISAMのみ使用)で、アクセス数とともにデータ量も増加して、インデックスがメモリに乗りきらず、Key\_reads/Key\_read_requests (以下key bufferヒット率という)が80%台まで落ちてしまっていた。

メモリを増設してkey\_buffer\_sizeを調子に乗ってでっかく取ってみたが、key bufferヒット率は99.9%になったものの、相変わらずディスクからの読み出しは多い。ここで、上に書いた仕組みに思い当たり、データ部分をキャッシュするOS用のメモリが不足しているのでは、と思って一旦key\_buffer\_sizeを少し減らしてみた。

  * インデックスのサイズ : 22GB
  * 従来 key\_buffer\_size : 4GB (OS物理メモリ12GB)
  * 増強後 key\_buffer\_size : 12GB (OS物理メモリ24GB)
  * その後 key\_buffer\_size : 8GB (OS物理メモリ24GB)

すると一目瞭然、以下のグラフの青枠部のようにディスクからの読み出しは半分以下に減った(赤線が設定を変更した時点)。

<a href="http://b.l0g.jp/mysql/do-not-set-too-much-keybuffer/attachment/la-io/" rel="attachment wp-att-1018"><img src="http://b.l0g.jp/wp-content/uploads/2012/04/la-io.png" alt="" title="ロードアベレージとディスクIOの変化" width="522" height="343" class="alignnone size-full wp-image-1018" /></a>

key\_buffer\_sizeを大きくしたからといってパフォーマンスが上がるというわけではないのである。上の例から分かるように、全インデックスがメモリに乗らなくても、よく使うインデックスがほぼメモリに乗っていれば問題ない。常に**key bufferヒット率が最大になり、ディスク読み出しが最小になる key\_buffer\_size を探ることが必要**ということだ。サイズはオンラインでも変更できる。ただし、コマンドを実行するとkey bufferの中身は一旦空になり、改めてキャッシュが構築されるため一時的にディスクIOが大量に発生するので注意(停止できるサーバならキャッシュウォームアップをした方がよい)。

```
  
mysql> set global key\_buffer\_size = バイトでのサイズ(1GBなら1073741824);
  
```

確認は show variables で。以下は4GBの例。

```
  
mysql> show variables like 'key\_buffer\_size';
  
+------+----+
  
| Variable_name | Value |
  
+------+----+
  
| key\_buffer\_size | 4294967296 |
  
+------+----+
  
1 row in set (0.00 sec)
  
```

そもそもインデックスがでか過ぎるのでシェイプアップすべきとか、テーブル分割を考えるべきといった別の解決方法も考えられるのだが、わかりやすいグラフが取れたのでひとつの例として。



