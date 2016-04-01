---
id: 1266
title: systemtapでファイルシステムキャッシュのヒット率を見る
date: 2012-06-18T12:23:01+00:00
author: doublemarket
layout: post
guid: http://b.l0g.jp/?p=1266
permalink: /linux/check-fs-cache-hitrate-with-stap/
categories:
  - Linux
---
<div class='wp_social_bookmarking_light'>
  <div class="wsbl_hatena_button">
    <a href="http://b.hatena.ne.jp/entry/http://b.l0g.jp/linux/check-fs-cache-hitrate-with-stap/" class="hatena-bookmark-button" data-hatena-bookmark-title="systemtapでファイルシステムキャッシュのヒット率を見る" data-hatena-bookmark-layout="standard" title="このエントリーをはてなブックマークに追加"> <img src="//b.hatena.ne.jp/images/entry-button/button-only@2x.png" alt="このエントリーをはてなブックマークに追加" width="20" height="20" style="border: none;" /></a>
  </div>
  
  <div class="wsbl_facebook_like">
    <div id="fb-root">
    </div><fb:like href="http://b.l0g.jp/linux/check-fs-cache-hitrate-with-stap/" layout="button_count" action="like" width="100" share="false" show_faces="false" ></fb:like>
  </div>
  
  <div class="wsbl_twitter">
    <a href="https://twitter.com/share" class="twitter-share-button"{count} data-url="http://b.l0g.jp/linux/check-fs-cache-hitrate-with-stap/" data-text="systemtapでファイルシステムキャッシュのヒット率を見る" data-via="dblmkt " data-lang="ja">Tweet</a>
  </div>
  
  <div class="wsbl_google_plus_one">
    <g:plusone size="medium" annotation="none" href="http://b.l0g.jp/linux/check-fs-cache-hitrate-with-stap/" ></g:plusone>
  </div>
</div>

<br class='wp_social_bookmarking_light_clear' />

<img src="http://b.l0g.jp/wp-content/uploads/2012/06/systemtaplogo1.png" title="systemtaplogo1" width="165" height="25" class="alignleft size-full wp-image-1280" style="border: 0px; margin-left: 8px; margin-right: 8px;" />

Linuxでは、自動的にメモリの空き容量をファイルシステムキャッシュ(以下単にキャッシュ)として使用するような仕組みが用意されている。MySQLでMyISAMを使う時のようにOSのキャッシュを使用するような仕組みを使っていたり、単にファイルをNFSで公開しているファイルサーバなどでは、このキャッシュがどの程度効くかによってかなりパフォーマンスが違ってくる。

多数のファイルが読みだされ、かつ読み出し対象ファイルの総容量が物理メモリ容量より多い場合(普通そうだと思うが)、どのくらいのキャッシュヒット率になっているかを知っておくのは、パフォーマンス改善の一助になる。しかし、あるファイルがキャッシュに載っているかどうかを調べたり、ファイルをキャッシュに載せたりするための単純なコマンドというのは実は存在していない(はず)。それで、

<blockquote class="twitter-tweet" width="500">
  <p>
    Linuxのファイルシステムキャッシュのヒット率って取得できないんだろうか。MyISAM使ってる時にどんくらいデータファイルがキャッシュヒットしてるか見たいなーと思ったけど、ググってみてもいい感じの情報が出てこない。カーネルソース読めって話かな…
  </p>
  
  <p>
    &mdash; doublemarket (@dblmkt) <a href="https://twitter.com/dblmkt/statuses/207373195425230848">May 29, 2012</a>
  </p>
</blockquote>



とつぶやいたら、

<blockquote class="twitter-tweet" width="500">
  <p>
    <a href="https://twitter.com/dblmkt">@dblmkt</a> VFS 周りの値とってごにょっとする感じになると思うます。こんな感じ？ <a href="http://t.co/5sa0HcvK">http://t.co/5sa0HcvK</a>
  </p>
  
  <p>
    &mdash; ishikawa84g＠驚きの白々しさ (@ishikawa84g) <a href="https://twitter.com/ishikawa84g/statuses/207391303351287808">May 29, 2012</a>
  </p>
</blockquote>



と教えてもらったので、<a title="Measuring Linux Filesystem Cache Hit Rate" href="http://sourceware.org/systemtap/wiki/WSCacheHitRate" target="_blank">リンク先のサイト</a>にあるスクリプトでキャッシュヒット率を確認してみた。@ishikawa84g 先生ありがとうございます～。

systemtapは、カーネル内部の挙動をトレースするためのツールの一種で、特別なスクリプトを書いてそれに従ってトレースすることができるので、色々な情報を取得することができる。

試してみたのはCentOS 6.2のマシン。systemtapがインストールされてなければインストールする。kernel-develも入っていないようであれば一緒に入れてしまうべし。

[bash]
  
$ sudo yum install systemtap kernel-devel
  
[/bash]

上記リンク先にあるスクリプトをファイルに保存し、そのまま実行するとエラーになってしまう。

[bash]
  
$ sudo stap cache-hit-rate.stp
  
semantic error: libdwfl failure (missing x86\_64 kernel/module debuginfo under &#8216;/lib/modules/2.6.18-164.el5/build&#8217;): No such file or directory while resolving probe point kernel.function(&#8220;vfs\_read&#8221;)
  
semantic error: no match while resolving probe point vfs.read
  
semantic error: no match while resolving probe point kernel.function(&#8220;generic\_make\_request&#8221;)
  
semantic error: no match while resolving probe point ioblock.request
  
Pass 2: analysis failed. Try again with another &#8216;&#8211;vp 01&#8217; option.
  
[/bash]

カーネルのdebuginfoがないためのエラーのようだ。debuginfoは専用のリポジトリからダウンロードする必要があるため、CentOSのdebuginfoリポジトリを登録する。debuginfoはそれなりにサイズもあるので、既に運用しているサーバにこれから入れるのは少しためらわれることもありそうだ。

/etc/yum.repos.d/CentOS-debuginfo.repo に以下の内容を書き込んで保存する。

[text]
  
[debuginfo]
  
name=CentOS-$releasever &#8211; DebugInfo
  
baseurl=http://debuginfo.centos.org/$releasever/$basearch/
  
gpgcheck=0
  
enabled=0
  
[/text]

kernel-debuginfoをインストールする。

[bash]
  
$ uname -a
  
Linux centos62-64-01 2.6.32-220.el6.x86\_64 #1 SMP Tue Dec 6 19:48:22 GMT 2011 x86\_64 x86\_64 x86\_64 GNU/Linux
  
$ sudo yum install &#8211;enablerepo=debuginfo kernel-debuginfo-2.6.32-220.el6.x86_64
  
[/bash]

パッケージが大きいので時間がかかる。この時、unameでカーネルバージョンを調べて使用しているカーネルと同じバージョンのdebuginfoを指定すること。指定しないとリポジトリにある最新のカーネルのdebuginfoがインストールされてしまう。

この状態でstapコマンドを実行すると、キャッシュのヒット率が5秒おきに15回表示される。

[bash]
  
$ sudo stap cache-hit-rate.stp
  
Starting&#8230;

Total Reads (KB) Cache Reads (KB) Disk Reads (KB) Miss Rate Hit Rate
                   
0 0 0 0.00% 0.00%
                   
0 0 0 0.00% 0.00%
                   
0 0 0 0.00% 0.00%
               
58802 66 58736 99.88% 0.11%
                   
0 0 0 0.00% 0.00%
                   
0 0 0 0.00% 0.00%
               
58785 58345 440 0.74% 99.25%
                   
0 0 0 0.00% 0.00%
                   
0 0 0 0.00% 0.00%
  
^C
  
Average Hit Rate: 49.67
  
Average Miss Rate: 50.32
  
[/bash]

キャッシュに読み込まれていないファイルを

[bash]
  
$ cat bigfile > /dev/null
  
[/bash]

とやって読みだしたのが8行目で、ここではファイルのほぼ全てをディスクから読みだしている。もう一度同じコマンドを実行すると、1度目に実行したcatで読みだされた際にファイルがキャッシュに読み込まれているので、ほとんどディスクからの読み出しがなく、11行目のようにほぼキャッシュヒット率100%となり一瞬でファイルの全てにアクセスできる。

スクリプトの作者も書いているが、LVMを使用している場合などにはヒット率が正しく出ない場合があるので、値は参考程度に考えておくべし。

参考
  
<a href="http://sourceware.org/systemtap/" title="SystemTap" target="_blank">SystemTap</a>
  
<a title="Linux のイントロスペクションと SystemTap" href="http://www.ibm.com/developerworks/jp/linux/library/l-systemtap/" target="_blank">Linux のイントロスペクションと SystemTap</a>
  
<a href="http://d.hatena.ne.jp/sh2/20100120" title="SystemTapでMySQLのDisk I/Oを分析する" target="_blank">SystemTapでMySQLのDisk I/Oを分析する</a>

<table  border="0" cellpadding="5">
  <tr>
    <td valign="top">
      <a href="http://www.amazon.co.jp/exec/obidos/ASIN/4873115019/l0gjp-22/ref=nosim/" target="_blank" rel="nofollow"><img src="http://ecx.images-amazon.com/images/I/51blzciVfIL._SL160_.jpg" border="0" alt="Linuxカーネル Hacks ―パフォーマンス改善、開発効率向上、省電力化のためのテクニック" style="border: 0px;" /></a>
    </td>
    
    <td valign="top">
      <font size="-1"><a href="http://www.amazon.co.jp/Linux%E3%82%AB%E3%83%BC%E3%83%8D%E3%83%AB-Hacks-%E2%80%95%E3%83%91%E3%83%95%E3%82%A9%E3%83%BC%E3%83%9E%E3%83%B3%E3%82%B9%E6%94%B9%E5%96%84%E3%80%81%E9%96%8B%E7%99%BA%E5%8A%B9%E7%8E%87%E5%90%91%E4%B8%8A%E3%80%81%E7%9C%81%E9%9B%BB%E5%8A%9B%E5%8C%96%E3%81%AE%E3%81%9F%E3%82%81%E3%81%AE%E3%83%86%E3%82%AF%E3%83%8B%E3%83%83%E3%82%AF-%E6%B1%A0%E7%94%B0-%E5%AE%97%E5%BA%83/dp/4873115019%3FSubscriptionId%3D15SMZCTB9V8NGR2TW082%26tag%3Dl0gjp-22%26linkCode%3Dxm2%26camp%3D2025%26creative%3D165953%26creativeASIN%3D4873115019" target="_blank" rel="nofollow">Linuxカーネル Hacks ―パフォーマンス改善、開発効率向上、省電力化のためのテクニック</a><img src="http://www.assoc-amazon.jp/e/ir?t=l0gjp-22&#038;l=ur2&#038;o=9" width="1" height="1" style="border: none;" alt="" /><br />池田 宗広 大岩 尚宏 島本 裕志 竹部 晶雄 平松 雅巳 高橋 浩和 </p> 
      
      <p>
        オライリージャパン 2011-07-26<br />売り上げランキング : 95520
      </p>
      
      <p>
        <a href="http://www.amazon.co.jp/Linux%E3%82%AB%E3%83%BC%E3%83%8D%E3%83%AB-Hacks-%E2%80%95%E3%83%91%E3%83%95%E3%82%A9%E3%83%BC%E3%83%9E%E3%83%B3%E3%82%B9%E6%94%B9%E5%96%84%E3%80%81%E9%96%8B%E7%99%BA%E5%8A%B9%E7%8E%87%E5%90%91%E4%B8%8A%E3%80%81%E7%9C%81%E9%9B%BB%E5%8A%9B%E5%8C%96%E3%81%AE%E3%81%9F%E3%82%81%E3%81%AE%E3%83%86%E3%82%AF%E3%83%8B%E3%83%83%E3%82%AF-%E6%B1%A0%E7%94%B0-%E5%AE%97%E5%BA%83/dp/4873115019%3FSubscriptionId%3D15SMZCTB9V8NGR2TW082%26tag%3Dl0gjp-22%26linkCode%3Dxm2%26camp%3D2025%26creative%3D165953%26creativeASIN%3D4873115019" target="_blank" rel="nofollow">Amazonで詳しく見る</a></font> <font size="-2">by <a href="http://www.goodpic.com/mt/aws/index.html" >G-Tools</a></font></td> </tr> </table> 
        
        <table  border="0" cellpadding="5">
          <tr>
            <td valign="top">
              <a href="http://www.amazon.co.jp/exec/obidos/ASIN/4873114047/l0gjp-22/ref=nosim/" target="_blank" rel="nofollow"><img src="http://ecx.images-amazon.com/images/I/41TmOAawDsL._SL160_.jpg" border="0" alt="Debug Hacks -デバッグを極めるテクニック&#038;ツール" style="border: 0px;" /></a>
            </td>
            
            <td valign="top">
              <font size="-1"><a href="http://www.amazon.co.jp/Debug-Hacks-%E3%83%87%E3%83%90%E3%83%83%E3%82%B0%E3%82%92%E6%A5%B5%E3%82%81%E3%82%8B%E3%83%86%E3%82%AF%E3%83%8B%E3%83%83%E3%82%AF-%E3%83%84%E3%83%BC%E3%83%AB-%E5%90%89%E5%B2%A1/dp/4873114047%3FSubscriptionId%3D15SMZCTB9V8NGR2TW082%26tag%3Dl0gjp-22%26linkCode%3Dxm2%26camp%3D2025%26creative%3D165953%26creativeASIN%3D4873114047" target="_blank" rel="nofollow">Debug Hacks -デバッグを極めるテクニック&#038;ツール</a><img src="http://www.assoc-amazon.jp/e/ir?t=l0gjp-22&#038;l=ur2&#038;o=9" width="1" height="1" style="border: none;" alt="" /><br />吉岡 弘隆 大和 一洋 大岩 尚宏 安部 東洋 吉田 俊輔 </p> 
              
              <p>
                オライリージャパン 2009-04-27<br />売り上げランキング : 190612
              </p>
              
              <p>
                <a href="http://www.amazon.co.jp/Debug-Hacks-%E3%83%87%E3%83%90%E3%83%83%E3%82%B0%E3%82%92%E6%A5%B5%E3%82%81%E3%82%8B%E3%83%86%E3%82%AF%E3%83%8B%E3%83%83%E3%82%AF-%E3%83%84%E3%83%BC%E3%83%AB-%E5%90%89%E5%B2%A1/dp/4873114047%3FSubscriptionId%3D15SMZCTB9V8NGR2TW082%26tag%3Dl0gjp-22%26linkCode%3Dxm2%26camp%3D2025%26creative%3D165953%26creativeASIN%3D4873114047" target="_blank" rel="nofollow">Amazonで詳しく見る</a></font> <font size="-2">by <a href="http://www.goodpic.com/mt/aws/index.html" >G-Tools</a></font></td> </tr> </table> 
                
                <hr />
                
                <p>
                  <b>海外の役立つブログ記事などを人力で翻訳して公開する<a href="https://yakst.com/ja">Yakst</a>というプロジェクトをやっています。よろしければそちらもどうぞ！</b>
                </p>