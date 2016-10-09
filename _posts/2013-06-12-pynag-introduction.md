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

```
  
$ sudo yum install http://ftp-srv2.kddilabs.jp/Linux/distributions/fedora/epel/6/x86_64/epel-release-6-8.noarch.rpm
  
$ sudo yum install pynag
  
```

pipが使えるならpipでもインストールできる。

```
  
$ sudo pip install pynag
  
```

## 設定ファイルのパース

pynagパッケージのModelモジュールを使えば、 /etc/nagios など代表的なパスから nagios.cfg を探し出して読み込んでくれる。含まれるホストとそのホストに関連づいたContact groupを表示する例。

```
  
from pynag import Model

# Get all hosts
  
all_hosts = Model.Host.objects.all
  
for i in all_hosts:
      
print i.host_name, i.contact_groups
  
```

## 設定書き換え

同じようにして、設定を保存することもできる。ホスト名がexamplehostのホストのContact groupを「mycontactgroup」にする例。

```
  
from pynag import Model

services = Model.Service.objects.filter(host_name='examplehost')
  
for i in services:
    
i.contactgroups = "mycontactgroup"
    
i.save()
  
```

## ステータスの取得

監視結果が保存されているstatus.datファイルのパースもしてくれるので、ホストやサービスのステータス取得もできる。

ホストグループ名とサービス名を引数に与えてやると、ホストグループに所属するホスト全てに関するサービスのステータスを返すスクリプト。

```
  
import sys
  
from pynag import Model
  
from pynag.Parsers import status

argvs = sys.argv
  
argc = len(argvs)
  
status = status()

if (argc != 3):
          
print
          
print("Usage: ./%s hostgroup service" % argvs[0])
          
print
          
sys.exit()

hostgroups = Model.Hostgroup.objects.filter(hostgroup_name=argvs[1])
  
for hostgroup in hostgroups:
          
print("Hostgroup: %s" % hostgroup.hostgroup_name)
          
print("Member:")
          
for host in hostgroup.members.split(','):
                  
print("%s %s" % (host, status.get_servicestatus(host, argvs[2])['current_state']))
  
```

status.datのパースにはやや時間がかかるので、監視対象サーバの台数が多いと使用に耐えない。そういう場合は、高速にステータスを取得できる<a href="http://mathias-kettner.de/checkmk_livestatus.html" title="MK Livestatus" target="_blank">MK Livestatus</a>と組み合わせることもできる。インストール方法は割愛するのでWebページを参照するべし。MK Livestatusを使って、上のスクリプトと同じようにホストグループとサービス名を引数に与えてホストのステータスを取得する例。

```

import sys
  
from pynag import Model
  
from pynag.Parsers import mk_livestatus

argvs = sys.argv
  
argc = len(argvs)
  
livestatus = mk_livestatus()

if (argc != 3):
          
print
          
print("Usage: ./%s hostgroup service" % argvs[0])
          
print
          
sys.exit()

hostgroups = Model.Hostgroup.objects.filter(hostgroup_name=argvs[1])
  
for hostgroup in hostgroups:
          
print("Hostgroup: %s" % hostgroup.hostgroup_name)
          
print("Member:")
          
for host in hostgroup.members.split(','):
                  
print(host)
                  
print(livestatus.get_service(host,argvs[2])['host_services_with_info'])
  
```

## コマンドラインから使う

Pythonのスクリプト内だけでなく、pynagコマンドとしてシェルから直接使うこともできる。where句で対象を絞ることが可能。

全ホストオブジェクトを表示する例。

```
  
$ pynag list where object_type=host
  
object_type shortname filename
  
——————————————————————————-
  
host server01 /etc/nagios/Default_collector/hosts.cfg
  
host server02 /etc/nagios/Default_collector/hosts.cfg
  
(中略)
  
———-123 objects matches search condition———————————-
  
```

MySQL_Connect_cというサービスのチェックコマンドを表示する例。

```
  
$ pynag list effective_command_line where service_description=MySQL_Connect_c
  
effective_command_line
  
——————————————————————————-
  
/usr/lib64/nagios/plugins/check_mysql_health -hostname -user nagios -password nagios -mode connection-time -database test -critical 1
  
———-1 objects matches search condition————————————
  
```

もちろんコマンドラインから設定の変更も可能。ホスト名server01をserver02に変更する例。

```
  
$ pynag update set host_name=server02 where host_name=server01 and object_type=host
  
```

where句にはlikeのような部分一致が使えないようだ。複雑なクエリを発行したいときは、pythonスクリプトとして作った方がいいかもしれない。コマンドラインでの使い方は<a href="https://github.com/pynag/pynag/wiki/Pynag-command-line-utility" title="pynag wiki" target="_blank">Wiki</a>にもう少し詳しく書いてある。

## Nagiosプラグイン作成時に使う

個人的に一番ツボなのがこの機能。自作のプラグインに、Nagiosのプラグインを書く際のガイドラインに準じた引数の処理などを簡単に加えることができる。

/proc/loadavgの中身から、ロードアベレージを引いてくる簡単な例。

```
  
#!/usr/bin/python
  
import os,sys

## プラグインのオプションを生成
  
np = Plugin()

## コマンドライン引数を追加
  
np.add_arg("l","load-file", "Enter a load average file", required=None)

## プラグインをアクティベート
  
np.activate()

## load-fileオプションが指定されたらそのファイルからLAを読み込む
  
## デバッグと、add_argメソッドのサンプルとして
  
if np['load-file']:
      
load_file = np['load-file']
  
else:
      
load_file = "/proc/loadavg"

## ちょっとしたエラーチェック
  
if not os.path.isfile(load_file):
      
np.nagios_exit(np.UNKNOWN, "Missing Load average file %s" % load_file)

## チェックに使う値を取得
  
current_load = os.popen("cat %s" % load_file).readline().split()[0]

## パフォーマンスデータを付加
  
np.add_perfdata("1min", current_load)

## チェック実行
  
np.check_range(current_load)
  
```

上のスクリプトをcheck_cpu.pyとして保存しておく。5行目のPlugin()で、プラグインとして必要なオプションが用意されるので、以下のようにスクリプトに引数を指定しないで実行しただけで、簡易ヘルプが表示される。

```
  
$ ./check_load.py
  
usage: check_load.py [options]

check_load.py: error: You must provide a WARNING and/or CRITICAL value
  
```

さらに、-hオプションをつけると、詳細なヘルプが表示される。

```
  
$ ./check_load.py -h
  
usage: check_load.py [options]

options:
    
-h, -help show this help message and exit
    
-l LOAD-FILE, -load-file=LOAD-FILE
                          
Enter a load average file
    
-v VERBOSE, -verbose=VERBOSE
                          
Verbosity Level
    
-H HOST, -host=HOST Target Host
    
-t TIMEOUT, -timeout=TIMEOUT
                          
Connection Timeout
    
-c CRITICAL, -critical=CRITICAL
                          
Critical Threshhold
    
-w WARNING, -warning=WARNING
                          
Warn Threshhold
  
```

ここで、スクリプト内の8行目、add_argメソッドで追加したオプションが、ヘルプの中に「-l LOAD-FILE, -load-file=LOAD-FILE」として表示されている。また、-Hでの監視対象ホスト指定や-c,-wによる閾値の指定オプションがはじめから用意されていることがわかる。自作スクリプトを書くたびにオプションのパースから書いていた事を考えれば、なんという簡単さ！

もちろん、ヘルプでみたとおりのオプションを与えてやれば、プラグインとして使用可能。

```
  
$ uptime
   
13:16:19 up 396 days, 22:32, 3 users, load average: 0.60, 1.08, 1.04

$ ./check_load.py -w 1 127.0.0.1
  
OK: 0.60 is inside warning=1 and critical=None | '1min'=0.60;;;;
  
```

元々やりたかったのは「同一ホストグループ内のある台数のチェック失敗まではWARNING、その台数以上のチェック失敗でCRITICALアラートを上げたい」ということだったのだが、以下のような短いプラグインスクリプトで実現できた。

```
  
#!/usr/bin/python

import sys
  
from pynag import Model
  
from pynag.Parsers import status
  
from pynag.Plugins import simple as Plugin

np = Plugin()
  
status = status()

np.add_arg("g", "hostgroup", "Hostgroup name", required=1)
  
np.add_arg("s", "service", "Service name", required=1)

np.activate()

errornum = 0
  
hostgroups = Model.Hostgroup.objects.filter(hostgroup_name=np['hostgroup'])
  
for hostgroup in hostgroups:
          
for host in hostgroup.members.split(','):
                  
if status.get_servicestatus(host, np['service'])['current_state'] != "0":
                          
errornum = errornum + 1
  
np.add_perfdata("WARN/CRIT", errornum)

np.check_range(errornum)
  
```

2台のチェックに失敗している時の例。

```
  
# 2より多いとwarn、3より多いとcrit
  
$ python check_wipeout.py -hostgroup=common_dbs -service=ifSpeed_eth0_w -w 2 -c 3
  
OK: 2 is inside warning=2 and critical=3 | 'WARN/CRIT'=2;;;;

$ echo $?
  


# 1より多いとwarn、3より多いとcrit
  
$ python check_wipeout.py -hostgroup=common_dbs -service=ifSpeed_eth0_w -w 1 -c 3
  
WARNING: 2 is outside warning range: 1 | 'WARN/CRIT'=2;;;;

$ echo $?
  
1

# 1より多いとcrit
  
$ python check_wipeout.py -hostgroup=common_dbs -service=ifSpeed_eth0_w -c 1
  
CRITICAL: 2 is outside critical range: 1 | 'WARN/CRIT'=2;;;;
  
$ echo $?
  
2
  
```

というわけで、pynagがあるとNagiosプラグインを書くのが超簡単になる！ pynag万歳！

ドキュメントは<a href="https://github.com/pynag/pynag/wiki" title="pynag wiki" target="_blank">Github上のWiki</a>を見るとよい。Wikiからだと分からない部分も多いが、<a href="https://github.com/pynag/pynag" title="pynag" target="_blank">ソース自体</a>はそれほど複雑ではないので読んでみればわかるのでは。

