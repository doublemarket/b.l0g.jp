---
id: 867
title: CentOSで各種ビープ音を鳴らさないようにする
date: 2011-08-18T23:59:08+00:00
author: doublemarket
layout: post
guid: http://b.l0g.jp/?p=867
permalink: /linux/beep-centos/
categories:
  - Linux
---
<div class='wp_social_bookmarking_light'>
  <div class="wsbl_hatena_button">
    <a href="http://b.hatena.ne.jp/entry/http://b.l0g.jp/linux/beep-centos/" class="hatena-bookmark-button" data-hatena-bookmark-title="CentOSで各種ビープ音を鳴らさないようにする" data-hatena-bookmark-layout="standard" title="このエントリーをはてなブックマークに追加"> <img src="//b.hatena.ne.jp/images/entry-button/button-only@2x.png" alt="このエントリーをはてなブックマークに追加" width="20" height="20" style="border: none;" /></a>
  </div>
  
  <div class="wsbl_facebook_like">
    <div id="fb-root">
    </div><fb:like href="http://b.l0g.jp/linux/beep-centos/" layout="button_count" action="like" width="100" share="false" show_faces="false" ></fb:like>
  </div>
  
  <div class="wsbl_twitter">
    <a href="https://twitter.com/share" class="twitter-share-button"{count} data-url="http://b.l0g.jp/linux/beep-centos/" data-text="CentOSで各種ビープ音を鳴らさないようにする" data-via="dblmkt " data-lang="ja">Tweet</a>
  </div>
  
  <div class="wsbl_google_plus_one">
    <g:plusone size="medium" annotation="none" href="http://b.l0g.jp/linux/beep-centos/" ></g:plusone>
  </div>
</div>

<br class='wp_social_bookmarking_light_clear' />

#### **＜ログイン時＞**

Xを起動するようにしていると(ランレベル5)、ログイン画面表示時にビープ音が鳴る。

「システム(System)」→「管理(Administration)」→「ログイン画面(Login Screen)」から「アクセシビリティ(Accessibility)」タブを開き、「ログイン画面を表示した時」のチェックを外す。

ログアウトし、次にログイン画面が表示されるときには、ビープ音は鳴らないようになっているはず。

#### **＜ターミナル使用時＞**

ターミナルを使用している時にタブを使ったりすると、ビープ音が鳴ることがある。

/etc/inputrc に以下の行を追加する(コメントアウトされた状態で既に記述があるはず)

[text]

set bell-style none

[/text]

ログインしなおすと、設定が有効になりビープ音は鳴らなくなる。

#### **＜vi＞**

vi(vim)を使用していると、コマンドを間違えた時などにビープ音が鳴る。

viの設定ファイル(ホームディレクトリの.vimrcや/etc/skel/.vimrcなど)に以下の行を追加する。

[text]

set visualbell

[/text]

記述してからviを起動すると、ビープ音は鳴らなくなる。

#### **＜全部のビープ音を鳴らないようにする＞**

ビープ音は、pcspkrというモジュールを経由して、マザーボード上のスピーカを動かして鳴らしている。このモジュール自体をロードしないようにしてしまえば、ビープ音は鳴らない。

/etc/modprobe.d/blacklist ファイルに以下の行を追加する。

[text]

blacklist pcspkr

[/text]

#### **＜VMware Playerを使用している場合＞**

VMware Playerを使用している時には、ゲストOSでビープ音が鳴るタイミングで、VMware Playerを通じて、ホストマシンのビープ音が鳴ってしまう。これを鳴らさないようにするには、VMware側の設定でビープを無効にしてしまう。

C:\Users\(ユーザ名)\AppData\Roaming\VMware\preferences.ini ファイルと、対象になる仮想マシンの設定ファイル(.vmx)に以下の行を追加する。

[text]

mks.noBeep = "TRUE"

[/text]

出典 : <a href="http://kb.vmware.com/kb/1664" target="_blank">VMwareのKnowledge base</a>

#### **＜Windowsでビープ音を鳴らさないようにする＞**

ついでにWindowsの場合も。ビープ音を鳴らすドライバを無効にしてしまう。以下、Windows 7での例。

  1. コントロールパネルなどから「デバイスマネージャ」を起動
  2.  「表示」→「非表示のデバイスの表示」
  3. ツリーから「プラグアンドプレイではないドライバ」→「Beep」を右クリックし、プロパティ
  4.  「ドライバー」タブで「停止」を選択

以上

* * *

**海外の役立つブログ記事などを人力で翻訳して公開する[Yakst](https://yakst.com/ja)というプロジェクトをやっています。よろしければそちらもどうぞ！**