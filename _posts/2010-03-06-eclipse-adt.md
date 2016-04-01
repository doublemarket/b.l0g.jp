---
id: 38
title: Eclipse + ADTインストール
date: 2010-03-06T14:54:05+00:00
author: doublemarket
layout: post
guid: http://b.l0g.jp/?p=38
permalink: /android/eclipse-adt/
categories:
  - Android
---
<div class='wp_social_bookmarking_light'>
  <div class="wsbl_hatena_button">
    <a href="http://b.hatena.ne.jp/entry/http://b.l0g.jp/android/eclipse-adt/" class="hatena-bookmark-button" data-hatena-bookmark-title="Eclipse + ADTインストール" data-hatena-bookmark-layout="standard" title="このエントリーをはてなブックマークに追加"> <img src="//b.hatena.ne.jp/images/entry-button/button-only@2x.png" alt="このエントリーをはてなブックマークに追加" width="20" height="20" style="border: none;" /></a>
  </div>
  
  <div class="wsbl_facebook_like">
    <div id="fb-root">
    </div><fb:like href="http://b.l0g.jp/android/eclipse-adt/" layout="button_count" action="like" width="100" share="false" show_faces="false" ></fb:like>
  </div>
  
  <div class="wsbl_twitter">
    <a href="https://twitter.com/share" class="twitter-share-button"{count} data-url="http://b.l0g.jp/android/eclipse-adt/" data-text="Eclipse + ADTインストール" data-via="dblmkt " data-lang="ja">Tweet</a>
  </div>
  
  <div class="wsbl_google_plus_one">
    <g:plusone size="medium" annotation="none" href="http://b.l0g.jp/android/eclipse-adt/" ></g:plusone>
  </div>
</div>

<br class='wp_social_bookmarking_light_clear' />

Javaを復習し始めると、ただ勉強するだけではなくてそれを形にしたくなってくる。さて、この頃トレンドのJavaを使った技術といえば、Google App Engineをプラットフォームに使ったWebアプリケーションか、それともAndroidかな？(どっちもGoogleだ)と考えていたところ、ソニー・エリクソンがXperiaというAndroid携帯を4月に出すということで、それを買うついでにAndroidアプリも作ってみようと思った、という至極短絡的な考え方で、Android SDKをインストールしてみた。もともとiPhoneが出た頃から、スマートフォンは欲しかったのだが、ソフトバンクに乗り換えたくなくてどうしようか迷っていたので、まあちょうどよい。

  1. Eclipseをダウンロードする。本家から国際化版をダウンロードしてきてLanguage Packをインストールしてもよいが、日本語化を進めるMergeDocというプロジェクトが、Pleiades All in One日本語化ディストリビューションというのを提供しており、これをインストールするのが楽そうなので、使ってみた。 
  
    <http://mergedoc.sourceforge.jp/>
  
    Eclipse 3.5.2 GalileoのUltimate(JREあり)をダウンロードし、展開するだけ。ただし、長いパス名のファイルが含まれているため、使う解凍ソフトに注意する必要があるようだ。詳しくはMergeDocのトップページ中程を参照のこと。
  2. 展開したフォルダには複数のフォルダがあるが、eclipseフォルダ内のeclipse.exeを実行する。お、本家のEclipseは青いスプラッシュスクリーンだが、こちらは赤か。途中でワークプレイスの設定をする画面が現れるので、適当に入力。
  3. 始めから日本語化された画面が起動する。「ヘルプ」から「新規ソフトウェアのインストール」を選択する。
  4. 「追加」ボタンをクリック。
  5. 「サイトの追加」画面が現れるので、名前欄にプラグインの概要(Eclipse Pluginとか)を入力し、ロケーションには<https://dl-ssl.google.com/android/eclipse/>と入力して「OK」を押す。
  6. 元の画面で、下のキャプチャ画面のようにDeveloper Toolsにチェックを入れ、「次へ」ボタンをクリック。ここにDeveloper Toolsが表示されないときは、追加ボタン左横のプルダウンメニューから、上の手順で追加したプラグインを選択する。
  7. 「完了」ボタンをクリック。
  8. プラグインのインストールが進み、途中で署名がないことの警告が出るが、「OK」をクリック。
  9. インストールが終わると、Eclipseの再起動を勧めるダイアログが出るので、素直に「はい」をクリック。

と、これだけで開発ができるわけではなく、Android SDKもインストールしないといけないのだが、これから出かけなくてはならないので、またあとで。

* * *

**海外の役立つブログ記事などを人力で翻訳して公開する[Yakst](https://yakst.com/ja)というプロジェクトをやっています。よろしければそちらもどうぞ！**