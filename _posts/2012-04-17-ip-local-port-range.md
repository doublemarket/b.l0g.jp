---
id: 998
title: TCP/IPの送信用ポート範囲を変更する
date: 2012-04-17T20:00:53+00:00
author: doublemarket
layout: post
guid: http://b.l0g.jp/?p=998
permalink: /linux/ip-local-port-range/
categories:
  - Linux
---

Linuxで、TCP/IPの送信時に使用するポートの範囲を決めるのは、net.ipv4.ip\_local\_port_range というカーネルパラメータ。手元のCentOSでは以下がデフォルト値になっていた。

```
  
\# sysctl net.ipv4.ip\_local\_port_range
  
net.ipv4.ip\_local\_port_range = 32768 61000
  
```

これだと、32768 から 61000 までの 28232 個の中からランダムに選ばれたポートを使って、サーバ側にアクセスする。このポートが足りなくなった場合、/etc/sysctl.conf に設定を書き込んでOSを再起動することで、範囲を変えられる。

```
  
\# /etc/sysctl.conf に以下を追記
  
net.ipv4.ip\_local\_port_range = 1024 65000
  
```

再起動

```
  
\# sysctl net.ipv4.ip\_local\_port_range
  
net.ipv4.ip\_local\_port_range = 1024 65000
  
```

これで1024から65000までのポートを使用できるようになる。

