---
id: 1572
title: CentOS 6になったらTomcatのJVMが仮想メモリを大量に確保するようになった
date: 2013-11-18T19:47:45+00:00
author: doublemarket
layout: post
guid: http://b.l0g.jp/?p=1572
permalink: /linux/big-mem-allocated-to-jvm-on-centos6/
categories:
  - Java
  - Linux
---
<div class='wp_social_bookmarking_light'>
  <div class="wsbl_hatena_button">
    <a href="http://b.hatena.ne.jp/entry/http://b.l0g.jp/linux/big-mem-allocated-to-jvm-on-centos6/" class="hatena-bookmark-button" data-hatena-bookmark-title="CentOS 6になったらTomcatのJVMが仮想メモリを大量に確保するようになった" data-hatena-bookmark-layout="standard" title="このエントリーをはてなブックマークに追加"> <img src="//b.hatena.ne.jp/images/entry-button/button-only@2x.png" alt="このエントリーをはてなブックマークに追加" width="20" height="20" style="border: none;" /></a>
  </div>
  
  <div class="wsbl_facebook_like">
    <div id="fb-root">
    </div><fb:like href="http://b.l0g.jp/linux/big-mem-allocated-to-jvm-on-centos6/" layout="button_count" action="like" width="100" share="false" show_faces="false" ></fb:like>
  </div>
  
  <div class="wsbl_twitter">
    <a href="https://twitter.com/share" class="twitter-share-button"{count} data-url="http://b.l0g.jp/linux/big-mem-allocated-to-jvm-on-centos6/" data-text="CentOS 6になったらTomcatのJVMが仮想メモリを大量に確保するようになった" data-via="dblmkt " data-lang="ja">Tweet</a>
  </div>
  
  <div class="wsbl_google_plus_one">
    <g:plusone size="medium" annotation="none" href="http://b.l0g.jp/linux/big-mem-allocated-to-jvm-on-centos6/" ></g:plusone>
  </div>
</div>

<br class='wp_social_bookmarking_light_clear' />

&nbsp;

CentOS 5+Tomcat 7+JDK 7+Apacheという組み合わせで動かしていたサーバ群に、OSだけCentOS 6にしてミドルウェアのバージョンは変えないサーバを何台か追加したところ、CentOS 6の方が、Tomcatが確保している仮想メモリ量が圧倒的にでかいことに気づいた。

ちなみに、どちらのサーバもヒープサイズは「-Xmn1024m -Xmx2048m -Xms2048m」となっており、本来なら2GBプラスアルファ(コードキャッシュ他の領域分)しかメモリは食わないはずだ。もちろん、ロードバランサではどちらのサーバにも同じ重みでアクセスを流していおり、負荷は同じのはず。

**CentOS 5のサーバ**

こちらはざっくり3GB弱の仮想メモリを確保していると出ている(左から5番目、VSZの列)。ヒープサイズから考えるとまあこんなものかというレベル。

[code]
  
\# ps auwx
  
USER PID %CPU %MEM VSZ RSS TTY STAT START TIME COMMAND
  
31234 10315 110 8.8 3134280 1447956 ? Sl Nov14 6870:09 /usr/local/java/bin/java -Djava.util.logging.config.file=/usr/l cal/tomcat/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -server -XX:MaxPermSize=128m -XX:PermSize=128m -XX:SurvivorRatio=2 -Xmn1024m -Xmx2048m -Xms2048m (後略)
  
[/code]

**CentOS 6のサーバ**

一方こちらは10GB弱ものメモリを確保していることになっている。ヒープサイズとの差分9GB近くはどっから出てきたのか？

[code]
  
\# ps auwx
  
USER PID %CPU %MEM VSZ RSS TTY STAT START TIME COMMAND
  
31234 19463 156 33.6 11270576 2708744 ? Sl Nov14 9532:10 /usr/local/java/bin/java -Djava.util.logging.config.file=/usr/local/tomcat/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -server -XX:MaxPermSize=128m -XX:PermSize=128m -XX:SurvivorRatio=2 -Xmn1024m -Xmx2048m -Xms2048m (後略)
  
[/code]

CentOS 6の方のサーバのTomcatのプロセスIDを指定してpmapコマンドでメモリの確保状況を見てみると、何やら同じ容量(64MBぐらい)を確保しているanonという行がたくさん表示されているのが目につく。これが悪さしているようだ。ちなみに、一番右の列には通常そのメモリを確保しているファイル名が出るが、anonというのは匿名で割り当てのみがされた領域を示しているようだ。

[code]
  
\# pmap -x 19463 | sort -k2 -n
  
Address Kbytes RSS Dirty Mode Mapping
  
(略)
  
00007f08d0218000 63392 0 0 &#8212;&#8211; [ anon ]
  
00007f0878215000 63404 0 0 &#8212;&#8211; [ anon ]
  
00007f099c208000 63456 0 0 &#8212;&#8211; [ anon ]
  
00007f08941fe000 63496 0 0 &#8212;&#8211; [ anon ]
  
00007f09341fb000 63508 0 0 &#8212;&#8211; [ anon ]
  
00007f091c1ea000 63576 0 0 &#8212;&#8211; [ anon ]
  
00007f08901e5000 63596 0 0 &#8212;&#8211; [ anon ]
  
00007f08fc1e3000 63604 0 0 &#8212;&#8211; [ anon ]
  
00007f09c01e0000 63616 0 0 &#8212;&#8211; [ anon ]
  
00007f094c1d8000 63648 0 0 &#8212;&#8211; [ anon ]
  
(略)
  
[/code]

調べてみたところ、この現象は、CentOS 6に含まれているglibc 2.11の挙動によるものだそうだ。glibc 2.10から、スレッドごとにメモリ空間を割り当てるような実装に変わったとのこと。なお、CentOS 5ではglibcは2.5。以下、<a title="glibc 2.10 news" href="http://udrepper.livejournal.com/20948.html" target="_blank">glibcのコミッタUlrich Drepper氏による2.10のchangelog解説</a>からの抜粋。

> glibcに対する今回の変更では、スレッドごとにメモリプールを作るようになった。これで多くの場面で間違った割り当てを排除することができる。メタデータは通常1つのスレッド内でしかアクセスしない(スレッドは割り当てられたコアから移動されないのが望ましい)。アドレス空間を吹き飛ばしてしまうようなメモリのハンドリングを防ぐため、メモリプールの使い過ぎに上限が設けられた。デフォルトでは、32ビットマシンではコアあたりメモリプール2つ、64ビットマシンではコアあたりメモリプール8つを生成する。

プールが確保するサイズは64MBであり、上の例のCentOS 6のマシンの場合、CPUは24コアの64ビットマシンなので、「24コア x 8 x 64MB」が仮想メモリとして確保されているため、VSSの列の値が大きくなっていたというわけだ。なお、このプール用のメモリはあくまで割り当てられただけで使用されているわけではない。そのため、実際に使われているメモリはRSS(Resident Set Size)の列に表示されているものである(当然だが)。

Tomcat動かしてて気づいたのでタイトルが「TomcatのJVMが～」となっているが、同じようなつくりのメモリを大きく確保するアプリケーションでは同じことになるのは言わずもがな。

参考1 : <a href="http://www.quora.com/Why-do-some-applications-use-significantly-more-virtual-memory-on-RHEL-6-compared-to-RHEL-5" target="_blank">Quoraのやりとり「なぜいくつかのアプリケーションはRHEL5と比べてRHEL6では明らかに大きな仮想メモリを使うのか？」</a>

参考2 : <a href="https://www.ibm.com/developerworks/community/blogs/kevgrig/entry/linux_glibc_2_10_rhel_6_malloc_may_show_excessive_virtual_memory_usage?lang=en" target="_blank">IBM developerWorksの記事「Linuxのglibc 2.10以上(RHEL6)のmallocは仮想メモリの使用量を過大に表示するのか」</a>

参考3 : <a href="http://stackoverflow.com/questions/561245/virtual-memory-usage-from-java-under-linux-too-much-memory-used" target="_blank">StackOverflowのやりとり「Linux上でのJavaの仮想メモリ使用量が大きすぎる」</a>  
解説が非常に詳しくてためになる。

参考4 : <a href="http://udrepper.livejournal.com/20948.html" target="_blank">上述glibcコミッタUlrich Drepper氏の記事</a>

* * *

**海外の役立つブログ記事などを人力で翻訳して公開する[Yakst](https://yakst.com/ja)というプロジェクトをやっています。よろしければそちらもどうぞ！**