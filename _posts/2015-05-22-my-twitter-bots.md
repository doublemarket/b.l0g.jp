---
id: 1664
title: Twitterボットを作るのがひそかな趣味
date: 2015-05-22T13:26:26+00:00
author: doublemarket
layout: post
guid: http://b.l0g.jp/?p=1664
permalink: /misc/my-twitter-bots/
categories:
  - 雑記
---
<div class='wp_social_bookmarking_light'>
  <div class="wsbl_hatena_button">
    <a href="http://b.hatena.ne.jp/entry/http://b.l0g.jp/misc/my-twitter-bots/" class="hatena-bookmark-button" data-hatena-bookmark-title="Twitterボットを作るのがひそかな趣味" data-hatena-bookmark-layout="standard" title="このエントリーをはてなブックマークに追加"> <img src="//b.hatena.ne.jp/images/entry-button/button-only@2x.png" alt="このエントリーをはてなブックマークに追加" width="20" height="20" style="border: none;" /></a>
  </div>
  
  <div class="wsbl_facebook_like">
    <div id="fb-root">
    </div><fb:like href="http://b.l0g.jp/misc/my-twitter-bots/" layout="button_count" action="like" width="100" share="false" show_faces="false" ></fb:like>
  </div>
  
  <div class="wsbl_twitter">
    <a href="https://twitter.com/share" class="twitter-share-button"{count} data-url="http://b.l0g.jp/misc/my-twitter-bots/" data-text="Twitterボットを作るのがひそかな趣味" data-via="dblmkt " data-lang="ja">Tweet</a>
  </div>
  
  <div class="wsbl_google_plus_one">
    <g:plusone size="medium" annotation="none" href="http://b.l0g.jp/misc/my-twitter-bots/" ></g:plusone>
  </div>
</div>

<br class='wp_social_bookmarking_light_clear' />

&nbsp;

プログラミングの勉強にと思ってTwitterボットを作ってみたら、ことあるごとに「あっ、これもボットになってたら便利そう」と思うようになってしまって、さらにいくつか作るという流れ。それなりに便利なのができると、ほっといてもフォロワーが増えていくのが楽しくなってしまう。今まで作ったボットのまとめ。

# 深田久弥bot  <a href="https://twitter.com/fukadakyuya_bot" target="_blank">@fukadakyuya_bot</a>

TLでフォロワーのみなさまと盛り上がったのがきっかけで、初めて作ったTwitterボット。ボットをフォローしているユーザが深田久弥の著書「<a href="http://www.amazon.co.jp/gp/product/4101220026/ref=as_li_ss_tl?ie=UTF8&camp=247&creative=7399&creativeASIN=4101220026&linkCode=as2&tag=l0gjp-22" rel="nofollow">日本百名山 (新潮文庫)</a><img style="border: none !important; margin: 0px !important;" src="http://ir-jp.amazon-adsystem.com/e/ir?t=l0gjp-22&l=as2&o=9&a=4101220026" alt="" width="1" height="1" border="0" />」に含まれる山名を含んだつぶやきをすると、同書の中からその山の特徴などを表した部分をリプライする。また、1日1回7：00にはランダムに抜粋をつぶやく。

<blockquote class="twitter-tweet" lang="ja">
  <p dir="ltr" lang="ja">
    <a href="https://twitter.com/doublemarket">@doublemarket</a> 夕方、日本海に沈む太陽の余映を受けて、白山が薔薇色に染まるひと時は、美しいものの究極であった。みるみるうちに薄鼠に暮れて行くまでの、暫くの間の微妙な色彩の推移は、この世のものとは思われなかった。
  </p>
  
  <p>
    — 深田久弥bot (@fukadakyuya_bot) <a href="https://twitter.com/fukadakyuya_bot/status/493185509611413504">2014, 7月 27</a>
  </p>
</blockquote>

最初はPHPで書いたのだがいつの間にかうまく動かなくなったりして、Rubyで書き換えた。ソースコードは<a href="https://github.com/doublemarket/twitterbot.rb" target="_blank">Githubに上げてある</a>。作った頃はRubyのエコシステムが良く分かってなくて、bundler使わずにやってたりして、自分でもどうやって動いてるのか理解してなかったなあｗ リプライする山名にどういう傾向があるのか気になったので、<a href="http://f.l0g.jp/" target="_blank">山名ごとにリプライ数を集計する仕組み</a>も作ってみた。基本的に富士山がダントツに多いのだが、例えば最近だと吾妻山や蔵王山(火山活動活発化)、立山(アルペンルート開通)といったように、急にリプライ数が増えた山があると、大体そこでは何かあったということが分かって面白い。

# 山の天気 <a href="https://twitter.com/yamatenki" target="_blank">@yamatenki</a>

毎朝7：30前後に、その週末に天気がいい(登山日和になりそうな)山を地域ごとにツイートする。 土曜と日曜それぞれ分けて判断するようになっている。祝日などの連休にも対応させたいが、めんどくさそうなので先送りしてしまっている。

<blockquote class="twitter-tweet" lang="ja">
  <p>
    【南アルプス】今週末、山に行くなら「甲斐駒ヶ岳」「仙丈岳」「鳳凰山」「北岳」「間ノ岳」、日曜日なら「塩見岳」「悪沢岳」「赤石岳」「聖岳」「光岳」の天気が良いようです。 <a href="http://t.co/YU8l9oRZUo">http://t.co/YU8l9oRZUo</a> — 山の天気 (@yamatenki) <a href="https://twitter.com/yamatenki/status/599341524011372545">2015, 5月 15</a>
  </p>
</blockquote>



山名を含んだメンションを送ると、少し詳しい天気予報をリプライしてくれる。

<blockquote class="twitter-tweet" lang="ja">
  <p dir="ltr" lang="ja">
    <a href="https://twitter.com/doublemarket">@doublemarket</a>【今週末の丹沢山】03/29土曜日は、くもり時々晴れ、降水確率は12時まで30%、18時まで30%。03/30日曜日は、くもり一時雨、降水確率は12時まで50%、18時まで50%。 (神奈川県西部) <a href="http://t.co/0Kh75x9Egg">http://t.co/0Kh75x9Egg</a>
  </p>
  
  <p>
    — 山の天気 (@yamatenki) <a href="https://twitter.com/yamatenki/status/448258476980981760">2014, 3月 25</a>
  </p>
</blockquote>

こちらも最初はPHPで作ったが今はRuby製。<a href="http://t.l0g.jp/" target="_blank">天気が一覧で見れるページ</a>も定期的に生成している。 深田久弥botと違ってうざくない作りなのもあってか、フォロワーが4600以上(2015年5月)もいて、自分の作ったボットの中では一番多い。

# hatebu english <a href="https://twitter.com/hatebu_e" target="_blank">@hatebu_e</a>

はてなブックマークが一定数以上付いた日本語以外の(正確には、タイトルと本文に全角文字を含まない)記事をツイートする。<a href="http://yakst.com/ja" target="_blank">Yakstという主に英語のブログ記事などを翻訳して公開するサイト</a>を解説しているのだが、そのネタ探しの足しになるかと思って作った。

<blockquote class="twitter-tweet" lang="ja">
  <p>
    GitHub Engineering (3 users) <a href="http://t.co/xWxAkjVCZI">http://t.co/xWxAkjVCZI</a> <a href="http://t.co/aDzjTh7OfY">http://t.co/aDzjTh7OfY</a> — hatebu english (@hatebu_e) <a href="https://twitter.com/hatebu_e/status/600682354470817792">2015, 5月 19</a>
  </p>
</blockquote>



誰得なのかよく分からないボットなので、フォロワーは少ないw

# AWS障害情報(全リージョン) <a href="https://twitter.com/awsstatusjp_all" target="_blank">@awsstatusjp_all</a>

# AWS障害情報(東京リージョン) <a href="https://twitter.com/awsstatusjp" target="_blank">@awsstatusjp</a>

仕事でAWSを本格的に使い始めたので、手軽にAWSのステータス情報を知れる手段がないかと思ったのだが、いい感じのボットがなかったので作った。時刻を日本時間に直し、定型的な文言はなるべく日本語に翻訳するようにしている。GCPとかのボットもなければ作ってみようかな。

<blockquote class="twitter-tweet" lang="ja">
  <p lang="ja" dir="ltr">
    日本時間2015年05月19日(火) 07:46:53 EC2 (サンパウロ) お知らせ Network Connectivity <a href="http://t.co/LMkNfK2ZYm">http://t.co/LMkNfK2ZYm</a>
  </p>
  
  <p>
    &mdash; AWS障害情報(全リージョン) (@awsstatusjp_all) <a href="https://twitter.com/awsstatusjp_all/status/600433202222608384">2015, 5月 18</a>
  </p>
</blockquote>



<blockquote class="twitter-tweet" lang="ja">
  <p dir="ltr" lang="ja">
    日本時間2015年05月16日(土) 00:36:27 EC2 (東京) 正常稼働中 [復旧済み] Network connectivity <a href="http://t.co/aq5bOVSJ4H">http://t.co/aq5bOVSJ4H</a>
  </p>
  
  <p>
    — AWS障害情報(東京リージョン) (@awsstatusjp) <a href="https://twitter.com/awsstatusjp/status/599237837637062656">2015, 5月 15</a>
  </p>
</blockquote>



ちなみに、プロジェクトでHipchatやSlackなどのチャットツールを使っている場合、それぞれのTwitter連携機能を使ってこのボットのツイートを拾うようにすれば、AWSの障害情報が流れてくるようになって便利。

ボットを作ると、プログラミングの基礎もだが、Twitter APIやスクレイピングのテクニックなど、作るボットに応じて色々な技術が必要とされるので、新しい言語を覚える人とか、そもそもプログラミング初心者にはもってこいの課題になる。オススメです。

* * *

**海外の役立つブログ記事などを人力で翻訳して公開する[Yakst](https://yakst.com/ja)というプロジェクトをやっています。よろしければそちらもどうぞ！**