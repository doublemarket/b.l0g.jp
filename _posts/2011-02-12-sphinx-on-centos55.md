---
id: 626
title: CentOS 5.5にsphinxをインストール
date: 2011-02-12T00:16:24+00:00
author: doublemarket
layout: post
guid: http://b.l0g.jp/?p=626
permalink: /dev/sphinx-on-centos55/
categories:
  - 開発
---
<div class='wp_social_bookmarking_light'>
  <div class="wsbl_hatena_button">
    <a href="http://b.hatena.ne.jp/entry/http://b.l0g.jp/dev/sphinx-on-centos55/" class="hatena-bookmark-button" data-hatena-bookmark-title="CentOS 5.5にsphinxをインストール" data-hatena-bookmark-layout="standard" title="このエントリーをはてなブックマークに追加"> <img src="//b.hatena.ne.jp/images/entry-button/button-only@2x.png" alt="このエントリーをはてなブックマークに追加" width="20" height="20" style="border: none;" /></a>
  </div>
  
  <div class="wsbl_facebook_like">
    <div id="fb-root">
    </div><fb:like href="http://b.l0g.jp/dev/sphinx-on-centos55/" layout="button_count" action="like" width="100" share="false" show_faces="false" ></fb:like>
  </div>
  
  <div class="wsbl_twitter">
    <a href="https://twitter.com/share" class="twitter-share-button"{count} data-url="http://b.l0g.jp/dev/sphinx-on-centos55/" data-text="CentOS 5.5にsphinxをインストール" data-via="dblmkt " data-lang="ja">Tweet</a>
  </div>
  
  <div class="wsbl_google_plus_one">
    <g:plusone size="medium" annotation="none" href="http://b.l0g.jp/dev/sphinx-on-centos55/" ></g:plusone>
  </div>
</div>

<br class='wp_social_bookmarking_light_clear' />

<a href="http://atnd.org/events/12450" target="_blank">2/12のSphinx翻訳ハッカソン</a>に行ってSymfony2のドキュメントの翻訳をさせていただこうと思っていたら、このところどっぷりはまっている仕事の方で呼び出されることになり、参加できなくなってしまった。予習として手元の環境にSphinxをインストールしたときのメモを残しておく。

といっても、パッケージ管理ツールを駆使(？)してあっという間。Sphinxのユーザ会のページには、MacOS XとUbuntuへのインストール方法は書いてあるが、CentOSへのインストール方法は書かれていないので、一応メモ。

yumでpythonのパッケージ管理ツールであるpipをインストール

> [root@hoge ~]# yum install python-pip python-setuptools

pipでsphinxをインストール。現状では1.0.7がインストールされるようだ。

> [root@hoge ~]# pip-python install sphinx
  
> Downloading/unpacking sphinx
  
> Downloading Sphinx-1.0.7.tar.gz (2.3Mb): 2.3Mb downloaded
  
> Running setup.py egg_info for package sphinx
  
> no previously-included directories found matching &#8216;doc/_build&#8217;
  
> Downloading/unpacking Pygments>=0.8 (from sphinx)
  
> Downloading Pygments-1.4.tar.gz (3.5Mb): 3.5Mb downloaded
  
> Running setup.py egg_info for package Pygments
  
> Downloading/unpacking Jinja2>=2.2 (from sphinx)
  
> Downloading Jinja2-2.5.5.tar.gz (438Kb): 438Kb downloaded
  
> (中略)
  
> Installing sphinx-build script to /usr/bin
  
> Installing sphinx-quickstart script to /usr/bin
  
> Installing sphinx-autogen script to /usr/bin
  
> Successfully installed docutils Jinja2 Pygments sphinx
  
> Cleaning up&#8230;
  
> [root@hoge ~]#

Sphinxユーザ会の「<a href="http://sphinx-users.jp/gettingstarted/index.html" target="_blank">Sphinxをはじめよう</a>」を見て、「プロジェクトの作成」まではあっさり完了。

yumでもsphinx(パッケージ名はpython-sphinx)をインストールできるが、<a href="http://twitter.com/#!/hidenorigoto" target="_blank">@hidenorigoto</a>さんのアドバイスで、pipを使ったインストール方法があることを知った。ありがとうございます。

**2011年3月13日追記**

epelリポジトリを有効にしていたからyumだけでインストールできた模様。epelリポジトリを追加していない場合は以下のコマンドで追加。

[text]

rpm -ivh http://ftp.riken.jp/Linux/fedora/epel/5/i386/epel-release-5-4.noarch.rpm

[/text]

※ パスの中で「5-4」はepelのバージョンによって変わる。

* * *

**海外の役立つブログ記事などを人力で翻訳して公開する[Yakst](https://yakst.com/ja)というプロジェクトをやっています。よろしければそちらもどうぞ！**