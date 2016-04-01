---
id: 1512
title: Nagiosのプラグイン作成や設定ファイル操作にpynagが超便利！
date: 2013-06-12T09:51:05+00:00
author: doublemarket
layout: post
guid: http://b.l0g.jp/?p=1512
permalink: /nagios/pynag-introduction/
categories:
  - Nagios
---

Nagiosの設定ファイルをパースする簡単な方法ないかなと思って調べてたら、<a href="http://pynag.org/" title="pynag.org" target="_blank">pynag</a>という超便利なパッケージ発見。日本語の情報ほとんどないけど、Fedoraの標準パッケージになっているってことは割と一般的なんだろうか。

  * Nagiosのプラグインを簡単に作りたい
  * Nagiosの設定ファイルをパースして値を取りたい
  * Nagiosの設定をコマンドラインやプログラムから取得・変更したい

って時に重宝する。pynagという名の通り、pythonで書かれている。公式ページのそのまんま訳に近いけど、使い方ご紹介。

## インストール

Fedoraの最近のバージョンには標準で含まれており、CentOSやRHELならepelを使えるようにしておけばyumでインストールできる。

[shell]
  
$ sudo yum install http://ftp-srv2.kddilabs.jp/Linux/distributions/fedora/epel/6/x86_64/epel-release-6-8.noarch.rpm
  
$ sudo yum install pynag
  
[/shell]

pipが使えるならpipでもインストールできる。

[shell]
  
$ sudo pip install pynag
  
[/shell]

## 設定ファイルのパース

pynagパッケージのModelモジュールを使えば、 /etc/nagios など代表的なパスから nagios.cfg を探し出して読み込んでくれる。含まれるホストとそのホストに関連づいたContact groupを表示する例。

[python]
  
from pynag import Model

\# Get all hosts
  
all_hosts = Model.Host.objects.all
  
for i in all_hosts:
      
print i.host\_name, i.contact\_groups
  
[/python]

## 設定書き換え

同じようにして、設定を保存することもできる。ホスト名がexamplehostのホストのContact groupを「mycontactgroup」にする例。

[python]
  
from pynag import Model

services = Model.Service.objects.filter(host_name=&#8217;examplehost&#8217;)
  
for i in services:
    
i.contactgroups = &#8220;mycontactgroup&#8221;
    
i.save()
  
[/python]

## ステータスの取得

監視結果が保存されているstatus.datファイルのパースもしてくれるので、ホストやサービスのステータス取得もできる。

ホストグループ名とサービス名を引数に与えてやると、ホストグループに所属するホスト全てに関するサービスのステータスを返すスクリプト。

[python]
  
import sys
  
from pynag import Model
  
from pynag.Parsers import status

argvs = sys.argv
  
argc = len(argvs)
  
status = status()

if (argc != 3):
          
print
          
print(&#8220;Usage: ./%s hostgroup service&#8221; % argvs[0])
          
print
          
sys.exit()

hostgroups = Model.Hostgroup.objects.filter(hostgroup_name=argvs[1])
  
for hostgroup in hostgroups:
          
print(&#8220;Hostgroup: %s&#8221; % hostgroup.hostgroup_name)
          
print(&#8220;Member:&#8221;)
          
for host in hostgroup.members.split(&#8216;,&#8217;):
                  
print(&#8220;%s %s&#8221; % (host, status.get\_servicestatus(host, argvs[2])[&#8216;current\_state&#8217;]))
  
[/python]

status.datのパースにはやや時間がかかるので、監視対象サーバの台数が多いと使用に耐えない。そういう場合は、高速にステータスを取得できる<a href="http://mathias-kettner.de/checkmk_livestatus.html" title="MK Livestatus" target="_blank">MK Livestatus</a>と組み合わせることもできる。インストール方法は割愛するのでWebページを参照するべし。MK Livestatusを使って、上のスクリプトと同じようにホストグループとサービス名を引数に与えてホストのステータスを取得する例。

[python]
  
import sys
  
from pynag import Model
  
from pynag.Parsers import mk_livestatus

argvs = sys.argv
  
argc = len(argvs)
  
livestatus = mk_livestatus()

if (argc != 3):
          
print
          
print(&#8220;Usage: ./%s hostgroup service&#8221; % argvs[0])
          
print
          
sys.exit()

hostgroups = Model.Hostgroup.objects.filter(hostgroup_name=argvs[1])
  
for hostgroup in hostgroups:
          
print(&#8220;Hostgroup: %s&#8221; % hostgroup.hostgroup_name)
          
print(&#8220;Member:&#8221;)
          
for host in hostgroup.members.split(&#8216;,&#8217;):
                  
print(host)
                  
print(livestatus.get\_service(host,argvs[2])[&#8216;host\_services\_with\_info&#8217;])
  
[/python]

## コマンドラインから使う

Pythonのスクリプト内だけでなく、pynagコマンドとしてシェルから直接使うこともできる。where句で対象を絞ることが可能。

全ホストオブジェクトを表示する例。

[shell]
  
$ pynag list where object_type=host
  
object_type shortname filename
  
&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8211;
  
host server01 /etc/nagios/Default_collector/hosts.cfg
  
host server02 /etc/nagios/Default_collector/hosts.cfg
  
(中略)
  
&#8212;&#8212;&#8212;-123 objects matches search condition&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-
  
[/shell]

MySQL\_Connect\_cというサービスのチェックコマンドを表示する例。

[shell]
  
$ pynag list effective\_command\_line where service\_description=MySQL\_Connect_c
  
effective\_command\_line
  
&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8211;
  
/usr/lib64/nagios/plugins/check\_mysql\_health &#8211;hostname &#8211;user nagios &#8211;password nagios &#8211;mode connection-time &#8211;database test &#8211;critical 1
  
&#8212;&#8212;&#8212;-1 objects matches search condition&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;
  
[/shell]

もちろんコマンドラインから設定の変更も可能。ホスト名server01をserver02に変更する例。

[shell]
  
$ pynag update set host\_name=server02 where host\_name=server01 and object_type=host
  
[/shell]

where句にはlikeのような部分一致が使えないようだ。複雑なクエリを発行したいときは、pythonスクリプトとして作った方がいいかもしれない。コマンドラインでの使い方は<a href="https://github.com/pynag/pynag/wiki/Pynag-command-line-utility" title="pynag wiki" target="_blank">Wiki</a>にもう少し詳しく書いてある。

## Nagiosプラグイン作成時に使う

個人的に一番ツボなのがこの機能。自作のプラグインに、Nagiosのプラグインを書く際のガイドラインに準じた引数の処理などを簡単に加えることができる。

/proc/loadavgの中身から、ロードアベレージを引いてくる簡単な例。

[python]
  
#!/usr/bin/python
  
import os,sys

\## プラグインのオプションを生成
  
np = Plugin()

\## コマンドライン引数を追加
  
np.add_arg(&#8220;l&#8221;,&#8221;load-file&#8221;, &#8220;Enter a load average file&#8221;, required=None)

\## プラグインをアクティベート
  
np.activate()

\## load-fileオプションが指定されたらそのファイルからLAを読み込む
  
\## デバッグと、add_argメソッドのサンプルとして
  
if np[&#8216;load-file&#8217;]:
      
load_file = np[&#8216;load-file&#8217;]
  
else:
      
load_file = &#8220;/proc/loadavg&#8221;

\## ちょっとしたエラーチェック
  
if not os.path.isfile(load_file):
      
np.nagios\_exit(np.UNKNOWN, &#8220;Missing Load average file %s&#8221; % load\_file)

\## チェックに使う値を取得
  
current\_load = os.popen(&#8220;cat %s&#8221; % load\_file).readline().split()[0]

\## パフォーマンスデータを付加
  
np.add\_perfdata(&#8220;1min&#8221;, current\_load)

\## チェック実行
  
np.check\_range(current\_load)
  
[/python]

上のスクリプトをcheck_cpu.pyとして保存しておく。5行目のPlugin()で、プラグインとして必要なオプションが用意されるので、以下のようにスクリプトに引数を指定しないで実行しただけで、簡易ヘルプが表示される。

[shell]
  
$ ./check_load.py
  
usage: check_load.py [options]

check_load.py: error: You must provide a WARNING and/or CRITICAL value
  
[/shell]

さらに、-hオプションをつけると、詳細なヘルプが表示される。

[shell]
  
$ ./check_load.py -h
  
usage: check_load.py [options]

options:
    
-h, &#8211;help show this help message and exit
    
-l LOAD-FILE, &#8211;load-file=LOAD-FILE
                          
Enter a load average file
    
-v VERBOSE, &#8211;verbose=VERBOSE
                          
Verbosity Level
    
-H HOST, &#8211;host=HOST Target Host
    
-t TIMEOUT, &#8211;timeout=TIMEOUT
                          
Connection Timeout
    
-c CRITICAL, &#8211;critical=CRITICAL
                          
Critical Threshhold
    
-w WARNING, &#8211;warning=WARNING
                          
Warn Threshhold
  
[/shell]

ここで、スクリプト内の8行目、add_argメソッドで追加したオプションが、ヘルプの中に「-l LOAD-FILE, &#8211;load-file=LOAD-FILE」として表示されている。また、-Hでの監視対象ホスト指定や-c,-wによる閾値の指定オプションがはじめから用意されていることがわかる。自作スクリプトを書くたびにオプションのパースから書いていた事を考えれば、なんという簡単さ！

もちろん、ヘルプでみたとおりのオプションを与えてやれば、プラグインとして使用可能。

[shell]
  
$ uptime
   
13:16:19 up 396 days, 22:32, 3 users, load average: 0.60, 1.08, 1.04

$ ./check_load.py -w 1 127.0.0.1
  
OK: 0.60 is inside warning=1 and critical=None | &#8216;1min&#8217;=0.60;;;;
  
[/shell]

元々やりたかったのは「同一ホストグループ内のある台数のチェック失敗まではWARNING、その台数以上のチェック失敗でCRITICALアラートを上げたい」ということだったのだが、以下のような短いプラグインスクリプトで実現できた。

[python]
  
#!/usr/bin/python

import sys
  
from pynag import Model
  
from pynag.Parsers import status
  
from pynag.Plugins import simple as Plugin

np = Plugin()
  
status = status()

np.add_arg(&#8220;g&#8221;, &#8220;hostgroup&#8221;, &#8220;Hostgroup name&#8221;, required=1)
  
np.add_arg(&#8220;s&#8221;, &#8220;service&#8221;, &#8220;Service name&#8221;, required=1)

np.activate()

errornum = 0
  
hostgroups = Model.Hostgroup.objects.filter(hostgroup_name=np[&#8216;hostgroup&#8217;])
  
for hostgroup in hostgroups:
          
for host in hostgroup.members.split(&#8216;,&#8217;):
                  
if status.get\_servicestatus(host, np[&#8216;service&#8217;])[&#8216;current\_state&#8217;] != &#8220;0&#8221;:
                          
errornum = errornum + 1
  
np.add_perfdata(&#8220;WARN/CRIT&#8221;, errornum)

np.check_range(errornum)
  
[/python]

2台のチェックに失敗している時の例。

[shell]
  
\# 2より多いとwarn、3より多いとcrit
  
$ python check\_wipeout.py &#8211;hostgroup=common\_dbs &#8211;service=ifSpeed\_eth0\_w -w 2 -c 3
  
OK: 2 is inside warning=2 and critical=3 | &#8216;WARN/CRIT&#8217;=2;;;;

$ echo $?
  


\# 1より多いとwarn、3より多いとcrit
  
$ python check\_wipeout.py &#8211;hostgroup=common\_dbs &#8211;service=ifSpeed\_eth0\_w -w 1 -c 3
  
WARNING: 2 is outside warning range: 1 | &#8216;WARN/CRIT&#8217;=2;;;;

$ echo $?
  
1

\# 1より多いとcrit
  
$ python check\_wipeout.py &#8211;hostgroup=common\_dbs &#8211;service=ifSpeed\_eth0\_w -c 1
  
CRITICAL: 2 is outside critical range: 1 | &#8216;WARN/CRIT&#8217;=2;;;;
  
$ echo $?
  
2
  
[/shell]

というわけで、pynagがあるとNagiosプラグインを書くのが超簡単になる！ pynag万歳！

ドキュメントは<a href="https://github.com/pynag/pynag/wiki" title="pynag wiki" target="_blank">Github上のWiki</a>を見るとよい。Wikiからだと分からない部分も多いが、<a href="https://github.com/pynag/pynag" title="pynag" target="_blank">ソース自体</a>はそれほど複雑ではないので読んでみればわかるのでは。<table norder=0> 

</table> 

* * *

**海外の役立つブログ記事などを人力で翻訳して公開する[Yakst](https://yakst.com/ja)というプロジェクトをやっています。よろしければそちらもどうぞ！**
