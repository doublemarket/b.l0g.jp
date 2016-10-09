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

<a href="http://atnd.org/events/12450" target="_blank">2/12のSphinx翻訳ハッカソン</a>に行ってSymfony2のドキュメントの翻訳をさせていただこうと思っていたら、このところどっぷりはまっている仕事の方で呼び出されることになり、参加できなくなってしまった。予習として手元の環境にSphinxをインストールしたときのメモを残しておく。

といっても、パッケージ管理ツールを駆使(？)してあっという間。Sphinxのユーザ会のページには、MacOS XとUbuntuへのインストール方法は書いてあるが、CentOSへのインストール方法は書かれていないので、一応メモ。

yumでpythonのパッケージ管理ツールであるpipをインストール

> [root@hoge ~]# yum install python-pip python-setuptools

pipでsphinxをインストール。現状では1.0.7がインストールされるようだ。

> [root@hoge ~]# pip-python install sphinx
  
> Downloading/unpacking sphinx
  
> Downloading Sphinx-1.0.7.tar.gz (2.3Mb): 2.3Mb downloaded
  
> Running setup.py egg_info for package sphinx
  
> no previously-included directories found matching 'doc/_build'
  
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
  
> Cleaning up…
  
> [root@hoge ~]#

Sphinxユーザ会の「<a href="http://sphinx-users.jp/gettingstarted/index.html" target="_blank">Sphinxをはじめよう</a>」を見て、「プロジェクトの作成」まではあっさり完了。

yumでもsphinx(パッケージ名はpython-sphinx)をインストールできるが、<a href="http://twitter.com/#!/hidenorigoto" target="_blank">@hidenorigoto</a>さんのアドバイスで、pipを使ったインストール方法があることを知った。ありがとうございます。

**2011年3月13日追記**

epelリポジトリを有効にしていたからyumだけでインストールできた模様。epelリポジトリを追加していない場合は以下のコマンドで追加。

```

rpm -ivh http://ftp.riken.jp/Linux/fedora/epel/5/i386/epel-release-5-4.noarch.rpm

```

※ パスの中で「5-4」はepelのバージョンによって変わる。

