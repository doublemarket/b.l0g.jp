---
id: 1335
title: Linuxのバッファキャッシュとページキャッシュの違いは？
date: 2012-10-24T22:56:15+00:00
author: doublemarket
layout: post
guid: http://b.l0g.jp/?p=1335
permalink: /linux/buffercache-and-page-cache/
categories:
  - Linux
---

Linuxが管理するキャッシュ領域には、バッファキャッシュとページキャッシュ領域があって、topコマンドや/proc/meminfoの「bufferes」「cached」という項目を見れば、現在のそれぞれのキャッシュ領域がどの程度なのかを確認することができる。

どちらも、ディスクの読み書きをキャッシュしてデータへのアクセスを高速化するという点では同じだが、それぞれがどのような意味で、どういう違いがあるのかについて、[Quoraに分かりやすい解説](http://www.quora.com/Linux-Kernel/In-Linux-what-is-the-major-difference-between-the-buffer-cache-and-the-page-cache-Why-were-they-separate-entities-in-older-kernels-Why-were-they-merged-later-on)があったので、翻訳してみた。

> ページキャッシュは、ファイルI/Oを最適化するために、ファイルのページをキャッシュする。バッファキャッシュは、ブロックI/Oを最適化するために、ディスクブロックをキャッシュする。
> 
> Linuxカーネル2.4よりも前では、2つのキャッシュは明白に違うものだった。ファイルはページキャッシュに、ディスクブロックはバッファキャッシュに載せられていた。ほとんどのファイルがディスク上のファイルシステムによって扱われているとすると、データは2回、すなわち両方のキャッシュそれぞれに取り扱われる。多くのUnixシステムはこれと同じようなパターンを採用している。
> 
> これはシンプルな実装だが、明らかに洗練されていないし、非効率である。Linuxカーネル2.4からは、2つのキャッシュのコンテンツは統合された。VM(Virtual Memory)サブシステムがI/Oを司るようになり、それはページキャッシュからデータを取り出す。キャッシュされたデータがファイルとブロックの両方のかたちをとる(ほとんどのデータがそうだが)場合、バッファキャッシュは単純にページキャッシュを指し示す。つまり、データは1つだけがメモリにキャッシュされていることになる。ページキャッシュは、ディスクキャッシュのことを考えた通りのものだ。それは、後続のI/Oを高速化するために、ディスクからデータをキャッシュする。
> 
> バッファキャッシュはいまだに残っているが、それは、カーネルは現在もページ単位ではなくブロック単位のブロックI/Oを行う必要があるからだ。ほとんどのブロックはファイルデータのかたちを取っているため、バッファキャッシュの多くはページキャッシュと同等である。しかし、少量のブロックデータ、例えばメタデータやrawブロックI/Oなどは、ファイルを前提にしたものではなく、もっぱらバッファキャッシュによって取り扱われる。

現在のかたちに至った経緯まで含めて説明できるのは、経験豊かな人ならではというべきか。

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
        
        <hr />
        
        <p>
          <b>海外の役立つブログ記事などを人力で翻訳して公開する<a href="https://yakst.com/ja">Yakst</a>というプロジェクトをやっています。よろしければそちらもどうぞ！</b>
        </p>
