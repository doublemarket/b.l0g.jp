---
id: 1346
title: Cactiで一定値以上になるとグラフが正常に描画されない時
date: 2012-11-12T19:20:28+00:00
author: doublemarket
layout: post
guid: http://b.l0g.jp/?p=1346
permalink: /cacti/broken-graph-with-big-value/
categories:
  - Cacti
---

Cactiを使っていると、下の画像のように、ある一定の値以上をグラフに描画できない時がある。この例の場合、青い線であらわされたout-boundのトラフィックが80Mbpsを超えるとグラフが描画されていない。

<p style="text-align: center;">
  <a href="http://b.l0g.jp/cacti/broken-graph-with-big-value/attachment/graph_image/" rel="attachment wp-att-1349"><img src="http://b.l0g.jp/wp-content/uploads/2012/11/graph_image.png" title="途切れたグラフ" width="449" height="137" class="size-full wp-image-1349 aligncenter" style="border: 0px;" /></a>
</p>

このような場合は、以下を確認する。

**トラフィックの場合、値を32ビットカウンタで取得していないか。**

64ビットカウンタで値を取得しないと、桁あふれが起きてグラフが正常に描画されない。ホストの編集画面(Console → Devices → ホストをクリック)のAssociated Data Queriesで「SNMP - Interface Statistics 64bit only」を選ぶ。

**そのグラフの扱える最大値を超えていないか。**

グラフの編集画面(Console → Data Source → ホスト名 - 分類 - 項目(たとえば hogehoge-db01 - Traffic - eth0 のような)) のMaximum Valueの値が、小さく設定されているようなら、これを大きくしてみる。

**RRDファイルに設定されている最大値を超えていないか。**

RRDファイルにも各値の最大値が設定されている。これを無限大に設定してやる。以下は、上の画像のようにトラフィックが途切れる場合の対策の例。

```
  
$ rrdtool info hogehoge-db01\_traffic\_in_200.rrd

filename = "mwap-r10\_traffic\_in_200.rrd"
  
rrd_version = "0003"
  
step = 300
  
last_update = 1352714073
  
ds[traffic_in].type = "COUNTER"
  
ds[traffic\_in].minimal\_heartbeat = 600
  
ds[traffic_in].min = 0.0000000000e+00
  
ds[traffic_in].max = 1.0000000000e+07 ← ここが最大値
  
ds[traffic\_in].last\_ds = "3293068416"
  
ds[traffic_in].value = 1.2345678909e+08
  
ds[traffic\_in].unknown\_sec = 0
  
ds[traffic_out].type = "COUNTER"
  
ds[traffic\_out].minimal\_heartbeat = 600
  
ds[traffic_out].min = 0.0000000000e+00
  
ds[traffic_out].max = 1.0000000000e+07 ← ここが最大値
  
ds[traffic\_out].last\_ds = "2054038296"
  
ds[traffic_out].value = 1.2345678909e+08
  
ds[traffic\_out].unknown\_sec = 0
  
(後略)
  
```

rrdtool tuneコマンドで変更

```
  
$ sudo rrdtool tune hogehoge-db01\_traffic\_in\_200.rrd -a traffic\_in:U
  
$ sudo rrdtool tune hogehoge-db01\_traffic\_in\_200.rrd -a traffic\_out:U
  
```

```
  
$ rrdtool info hogehoge-db01\_traffic\_in_200.rrd

filename = "mwap-r10\_traffic\_in_200.rrd"
  
rrd_version = "0003"
  
step = 300
  
last_update = 1352714073
  
ds[traffic_in].type = "COUNTER"
  
ds[traffic\_in].minimal\_heartbeat = 600
  
ds[traffic_in].min = 0.0000000000e+00
  
ds[traffic_in].max = NaN ← 無限大に変わってる
  
ds[traffic\_in].last\_ds = "3293068416"
  
ds[traffic_in].value = 1.2345678909e+08
  
ds[traffic\_in].unknown\_sec = 0
  
ds[traffic_out].type = "COUNTER"
  
ds[traffic\_out].minimal\_heartbeat = 600
  
ds[traffic_out].min = 0.0000000000e+00
  
ds[traffic_out].max = NaN ← 無限大に変わってる
  
ds[traffic\_out].last\_ds = "2054038296"
  
ds[traffic_out].value = 1.2345678909e+08
  
ds[traffic\_out].unknown\_sec = 0
  
(後略)
  
```

