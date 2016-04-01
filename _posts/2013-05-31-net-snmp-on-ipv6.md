---
id: 1472
title: IPv6環境でnet-snmpを使う
date: 2013-05-31T12:25:46+00:00
author: doublemarket
layout: post
guid: http://b.l0g.jp/?p=1472
permalink: /linux/net-snmp-on-ipv6/
categories:
  - Linux
  - Nagios
---

IPv6環境でSNMPで情報を引っ張ってきたいのだが、net-snmpをインストールしてsnmpdを起動したが、getできない。
  
監視サーバをnagios01、監視対象ホストをclient01とした時の例。

[text]
  
client01$ sudo yum install net-snmp net-snmp-utils
  
client01$ sudo /etc/init.d/snmpd start
  
[/text]

[text]
  
nagios01$ snmpwalk -v 1 -c public client01 UCD-SNMP-MIB::laLoad.1
  
No log handling enabled &#8211; turning on stderr logging
  
getaddrinfo: client01 Name or service not known
  
snmpwalk: Unknown host (client01)
  
[/text]

snmpwalkなどでIPv6のサーバへ問い合わせる場合は、ホスト名の前に「udp6」を明示的に指定する必要があるようだ。しかし、

[text]
  
nagios01$ snmpwalk -v 1 -c public udp6:client01 UCD-SNMP-MIB::laLoad.1
  
Timeout: No Response from udp6:client01
  
[/text]

タイムアウトになってしまう。ちなみに、<a href="http://www.net-snmp.org/docs/NEWS.html" target="_blank">net-snmpは5.0以降IPv6に対応している</a>ということなので、5.0以降がインストールされていれば、snmpwalkコマンドもIPv6を扱えるはず。
  
クライアント側を見てみる。

[text]
  
client01$ netstat -lanp
  
Active Internet connections (servers and established)
  
Proto Recv-Q Send-Q Local Address Foreign Address State PID/Program name
  
(中略)
  
udp 0 0 0.0.0.0:161 0.0.0.0:* 12403/snmpd
  
[/text]

0.0.0.0:161ってことは、IPv4だけで、IPv6でリッスンしていない。
  
設定ファイルを見てみる。

[text]
  
client01$ cat /etc/snmp/snmpd.conf
  
(以下抜粋)
  
com2sec notConfigUser localhost public
  
com2sec notConfigUser nagios01 public
  
group notConfigGroup v1 nogConfigUser
  
access notConfigGroup &#8220;&#8221; any noauth exact systemview none none
  
syslocation Unknown (edit /etc/snmp/snmpd.conf)
  
syscontact Root <root@localhost> (configure /etc/snmp/snmp.local.conf)
  
view all included .1 80
  
dontLogTCPWrappersConnects yes
  
[/text]

どうやら、デフォルトの設定では、IPv4しかリッスンしない設定らしい。以下のように、明示的にv6でも使えるように追記。

[text]
  
\# 以下の4行を追記(下の2行は実際の設定に応じて変更)
  
agentaddress udp:161
  
agentaddress udp6:161

com2sec6 notConfigUser localhost public
  
com2sec6 notConfigUser nagios01 public
  
[/text]

snmpd を再起動すると、v6でもリッスンするようになった。

[text]
  
client01$ sudo /etc/init.d/snmpd restart
  
client01$ netstat -lanp
  
Active Internet connections (servers and established)
  
Proto Recv-Q Send-Q Local Address Foreign Address State PID/Program name
  
(中略)
  
udp 0 0 0.0.0.0:161 0.0.0.0:* 12442/snmpd
  
udp 0 0 :::161 :::* 12442/snmpd
  
[/text]

これで値を取れるようになった。

[text]
  
nagios01$ snmpwalk -v 1 -c public udp6:client01 UCD-SNMP-MIB::laLoad.1
  
UCD-SNMP-MIB::laLoad.1 = STRING: 0.03
  
[/text]

**まとめ**

IPv6環境でnet-snmpを使うときは

  * net-snmp 5.0以降を使う 
      * snmpd.confで、agentaddress udp6:161とcom2sec6を設定する 
          * snmpwalkなどで値を取る時はアドレスの前にudp6:をつける </ul> 
            
            
            * * *
            
            **海外の役立つブログ記事などを人力で翻訳して公開する[Yakst](https://yakst.com/ja)というプロジェクトをやっています。よろしければそちらもどうぞ！**
