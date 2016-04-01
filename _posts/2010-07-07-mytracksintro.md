---
id: 393
title: 登山記録に使えるGPSアプリ My Tracks
date: 2010-07-07T00:42:17+00:00
author: doublemarket
layout: post
guid: http://b.l0g.jp/?p=393
permalink: /android/mytracksintro/
categories:
  - Android
---

自分がAndroid携帯(Sony ErricsonのXperia)を買ったのは、スマートフォンならではの自由度の高さ(画面の大きさやセンサーの多さなど)を活かしたアプリケーションを、自分で作って使ってみたいと思ったことがきっかけである。スマートフォンといえばiPhoneじゃない？という人が多いと思うが、登山によく行く自分にとって、少なくとも現時点ではドコモとauと比較するとかなり電波状況の悪いソフトバンクテレコムに乗り換えるという選択肢はどうしても取れなかった。また、iPhoneアプリを作るには、まずMacを買わなければならないというのも、Android携帯を選んだ理由である。現時点で、良好な電波状況と端末の完成度の両方を満たす端末は、Xperia以外にないといえる(auのIS01もありという人もいるかもしれないが)。と思っているのだが、なぜか自分の登山仲間のスマフォ持ちはiPhoneばっかり。へっ！iPhoneなんてソフバンで電波届かないから、どうせ山に登って「山頂なう！」ってつぶやきもできないくせに！(笑)

具体的に作ってみたかったアプリとしては、登山道を歩きながらGPSデータを保存してくれる、GPSロガーである。ところが、Xperiaを入手してあれこれと初歩的なアプリを作っているうちに、My Trackというアプリを見つけた。

[My Tracks for Android](http://mytracks.appspot.com/)

これはまさに欲しかったアプリではないか！ 事前に調べた限りではGPSロガー的なアプリは見つからなかったのだが(iPhoneアプリはいくつか見つけた)、探し方が悪かっただけらしい。そんなわけで、自分で作らなくても既にあったので、あっさりと自分のAndroidアプリ制作意欲はほぼゼロまで削がれた。2010年5月からはソースコードも公開されたため、改変も可能になった。

[mytracks - Project Hosting on Google Code](http://code.google.com/p/mytracks/)

使い方は簡単で、Record Trackというメニューを選んで歩き始めると、一定の間隔でGPSの情報を記録し、Googleマップ上に表示してくれるというのが基本機能。以下の画面は乾徳山に登った時のコースの一部。機種や電波の受信状況にもよるだろうが、結構高精度で記録が取れる。この時はRecord Trackするのを忘れていて、一部のルートしか記録できなかった。

[<img src="http://b.l0g.jp/wp-content/uploads/01-168x300.png" alt="" title="01" width="168" height="300" class="alignnone size-medium wp-image-394" />](http://b.l0g.jp/wp-content/uploads/2010/07/01.png)

Stop Recordメニューを選べば、記録終了。Googleマップでは、拡大すると等高線も表示されるので、地形なども推測できる。

[<img src="http://b.l0g.jp/wp-content/uploads/02-168x300.png" alt="" title="02" width="168" height="300" class="alignnone size-medium wp-image-395" />](http://b.l0g.jp/wp-content/uploads/2010/07/02.png)

画面中央左右の矢印をタップすると、他の情報も見られる。右をタップすると、歩いた距離や最高・平均速度、最低・最高標高、標高差などが確認できる。

[<img src="http://b.l0g.jp/wp-content/uploads/03-168x300.png" alt="" title="03" width="168" height="300" class="alignnone size-medium wp-image-396" />](http://b.l0g.jp/wp-content/uploads/2010/07/03.png)

さらに右をタップすると、横軸に距離、縦軸に標高と歩行速度のグラフが見られる。緑が標高で、この時は1.5kmほど歩く間に1580mぐらいから1800m近くまで登った様子がわかる。青が歩行速度で、見方は…単位がいまいちよくわからない。

[<img src="http://b.l0g.jp/wp-content/uploads/04-168x300.png" alt="" title="04" width="168" height="300" class="alignnone size-medium wp-image-397" />](http://b.l0g.jp/wp-content/uploads/2010/07/04.png)

もちろんGPSのトラックデータはPCにコピーすることができる。そのデータをGoogle Earthやカシミール3Dに重ねあわせてみるとまた面白いだろう。

このアプリ自体は、なにも登山にだけ用途が限られているわけではなく、ウォーキングやジョギングの際などにも色々使い道が考えられる。日本語化はされていないようだが、「Android携帯を持っていて登山やウォーキングをする」という人は日本にはまだまだ少ないだろうから、そんなものかもしれない。オープンソースなので自分で翻訳してもいいのだが、まずは機能が分かっていないので、もう少しあちこちの山に行く時に使ってみようと思っている。

* * *

**海外の役立つブログ記事などを人力で翻訳して公開する[Yakst](https://yakst.com/ja)というプロジェクトをやっています。よろしければそちらもどうぞ！**
