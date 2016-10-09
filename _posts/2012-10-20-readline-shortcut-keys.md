---
id: 1304
title: readlineのショートカットまとめ
date: 2012-10-20T17:11:42+00:00
author: doublemarket
layout: post
guid: http://b.l0g.jp/?p=1304
permalink: /shell/readline-shortcut-keys/
categories:
  - シェル
---

[<img src="http://b.l0g.jp/wp-content/uploads/2012/10/gnu-head-sm.jpg" alt="" title="gnu-head-sm" width="129" height="122" class="alignleft size-full wp-image-1310" style="border: 0px; margin-left: 8px; margin-right: 8px;" />](http://tiswww.case.edu/php/chet/readline/rltop.html)

<blockquote class="twitter-tweet tw-align-center" lang="ja">
  <p>
    mysqlコマンドのプロンプトで使えるキーボードショートカットについて探してたら、readline使ってるからbashとかと同じになるのか。今さら気付いて恥ずかしいが当たり前といえば当たり前かｗ
  </p>
  
  <p>
    &mdash; doublemarketさん (@dblmkt) <a href="https://twitter.com/dblmkt/status/259533796645425153" data-datetime="2012-10-20T05:57:33+00:00">10月 20, 2012</a>
  </p>
</blockquote>



ということで、調べたついでなのでreadlineのキーボードショートカットをまとめてみた。知らなかったのもあって意外と勉強になる。なお、使っているターミナルによってはAltの代わりに"Esc→キー"を使う必要がある。

ちなみに、readlineを使っているのはbash, zsh, ksh, mysql, psql, ftp, bcなど色々あるがこれらのコマンドラインツールでは基本的に以下のショートカットキーが使える(ソフトウェアによっては全部が全部使えるわけではない)。

## カーソル移動系

さすがにここは定番すぎる。

<table class="article">
  <tr>
    <th>
      キー入力
    </th>
    
    <th>
      動作
    </th>
  </tr>
  
  <tr>
    <td>
      Ctrl-b
    </td>
    
    <td>
      カーソルを左に1文字移動
    </td>
  </tr>
  
  <tr>
    <td>
      Ctrl-f
    </td>
    
    <td>
      カーソルを右に1文字移動
    </td>
  </tr>
  
  <tr>
    <td>
      Alt-b
    </td>
    
    <td>
      カーソルを左に1単語移動
    </td>
  </tr>
  
  <tr>
    <td>
      Alt-f
    </td>
    
    <td>
      カーソルを右に1単語移動
    </td>
  </tr>
  
  <tr>
    <td>
      Ctrl-a
    </td>
    
    <td>
      カーソルを行頭に移動
    </td>
  </tr>
  
  <tr>
    <td>
      Ctrl-e
    </td>
    
    <td>
      カーソルを行末に移動
    </td>
  </tr>
  
  <tr>
    <td>
      Ctrl-x-x
    </td>
    
    <td>
      入力を始めた位置と現時点のカーソル位置を交互に移動
    </td>
  </tr>
</table>

## 削除系

この辺は、知っておくと素早くコマンドを修正できるがなかなか覚えられない。

<table class="article">
  <tr>
    <th>
      キー入力
    </th>
    
    <th>
      動作
    </th>
  </tr>
  
  <tr>
    <td>
      Back space
    </td>
    
    <td>
      カーソルの左の文字を削除
    </td>
  </tr>
  
  <tr>
    <td>
      Delete<br />Ctrl-d
    </td>
    
    <td>
      カーソル位置の文字を削除
    </td>
  </tr>
  
  <tr>
    <td>
      Ctrl-u
    </td>
    
    <td>
      行頭からカーソル位置までの文字を削除
    </td>
  </tr>
  
  <tr>
    <td>
      Ctrl-k
    </td>
    
    <td>
      カーソル位置から行末までの文字を削除
    </td>
  </tr>
  
  <tr>
    <td>
      Alt-d
    </td>
    
    <td>
      カーソル位置から次のスペースまでの文字を削除
    </td>
  </tr>
  
  <tr>
    <td>
      Ctrl-w
    </td>
    
    <td>
      直前の空白からカーソル位置の1文字前までの文字を削除
    </td>
  </tr>
  
  <tr>
    <td>
      Ctrl-y
    </td>
    
    <td>
      直前に上の削除コマンドで削除した文字をカーソル位置にペースト
    </td>
  </tr>
  
  <tr>
    <td>
      Alt-y
    </td>
    
    <td>
      n回前に削除した文字をカーソル位置にペースト(入力するたびに前に戻っていく)
    </td>
  </tr>
</table>

## ヒストリ系

最後の2つ、引数をペーストするのは知らなかった。そんなに使う感じしないけど。

<table class="article">
  <tr>
    <th>
      キー入力
    </th>
    
    <th>
      動作
    </th>
  </tr>
  
  <tr>
    <td>
      Ctrl-p
    </td>
    
    <td>
      1回前に実行されたコマンドを表示
    </td>
  </tr>
  
  <tr>
    <td>
      Ctrl-n
    </td>
    
    <td>
      1回後に実行されたコマンドを表示
    </td>
  </tr>
  
  <tr>
    <td>
      Alt->
    </td>
    
    <td>
      元々入力されていたコマンドを表示
    </td>
  </tr>
  
  <tr>
    <td>
      Ctrl-r
    </td>
    
    <td>
      現在表示されているものより前に実行されたコマンドをインクリメンタルサーチ
    </td>
  </tr>
  
  <tr>
    <td>
      Ctrl-s
    </td>
    
    <td>
      現在表示されているものより後に実行されたコマンドをインクリメンタルサーチ
    </td>
  </tr>
  
  <tr>
    <td>
      Ctrl-J
    </td>
    
    <td>
      インクリメンタルサーチを終了
    </td>
  </tr>
  
  <tr>
    <td>
      Ctrl-G
    </td>
    
    <td>
      インクリメンタルサーチを中止して元に戻る
    </td>
  </tr>
  
  <tr>
    <td>
      Alt-Ctrl-y
    </td>
    
    <td>
      直前のコマンドの第1引数をカーソル位置にペースト
    </td>
  </tr>
  
  <tr>
    <td>
      Alt-.<br />Alt-_
    </td>
    
    <td>
      直前のコマンドの最後の引数をカーソル位置にペースト
    </td>
  </tr>
</table>

## Undo系

<table class="article">
  <tr>
    <th>
      キー入力
    </th>
    
    <th>
      動作
    </th>
  </tr>
  
  <tr>
    <td>
      Ctrl-_<br />Ctrl-x Ctrl-u
    </td>
    
    <td>
      入力しているコマンドを全部削除
    </td>
  </tr>
  
  <tr>
    <td>
      Alt-r
    </td>
    
    <td>
      入力している行を全部削除(複数行入力している時にその行だけを消す)
    </td>
  </tr>
  
  <tr>
    <td>
      Ctrl-l
    </td>
    
    <td>
      表示を全部消して現在入力している行を一番上に表示
    </td>
  </tr>
</table>

## オートコンプリート系

Alt-*とかどういう時に使う想定なんだろうか。

<table class="article">
  <tr>
    <th>
      キー入力
    </th>
    
    <th>
      動作
    </th>
  </tr>
  
  <tr>
    <td>
      Tab
    </td>
    
    <td>
      入力をオートコンプリート
    </td>
  </tr>
  
  <tr>
    <td>
      Alt-?<br />Tab Tab
    </td>
    
    <td>
      オートコンプリートの候補を表示
    </td>
  </tr>
  
  <tr>
    <td>
      Alt-*
    </td>
    
    <td>
      オートコンプリートの候補を全部入力
    </td>
  </tr>
</table>

## 入れ替え系

このコマンドどちらも知らなかったが、オプションの順序を間違えた時に簡単に入れ替えたりするのにとても重宝する！

<table class="article">
  <tr>
    <th>
      キー入力
    </th>
    
    <th>
      動作
    </th>
  </tr>
  
  <tr>
    <td>
      Ctrl-t
    </td>
    
    <td>
      カーソルの直前の文字とカーソル位置の文字を入れ替える
    </td>
  </tr>
  
  <tr>
    <td>
      Alt-t
    </td>
    
    <td>
      カーソル位置の前の単語とカーソル位置の単語を入れ替える
    </td>
  </tr>
</table>

## その他編集系

これも知らなかった。そんなに使用頻度多くない人もいるだろうが、SQLを入力する時に予約語を大文字で入力したい人とか便利なんじゃないか。

<table class="article">
  <tr>
    <th>
      キー入力
    </th>
    
    <th>
      動作
    </th>
  </tr>
  
  <tr>
    <td>
      Alt-u
    </td>
    
    <td>
      カーソル位置の単語の、カーソル位置より後を大文字にし、カーソルは単語の最後に移動
    </td>
  </tr>
  
  <tr>
    <td>
      Alt-l
    </td>
    
    <td>
      カーソル位置の単語の、カーソル位置より後を小文字にし、カーソルは単語の最後に移動
    </td>
  </tr>
  
  <tr>
    <td>
      Alt-c
    </td>
    
    <td>
      カーソル位置の単語の先頭文字を大文字にし、カーソルは単語の最後に移動
    </td>
  </tr>
</table>

参考1 : [Readline shortcuts](http://www.bigsmoke.us/readline/shortcuts)
  
参考2 : [GNU Readline Library Documentation](http://tiswww.case.edu/php/chet/readline/readline.html)

