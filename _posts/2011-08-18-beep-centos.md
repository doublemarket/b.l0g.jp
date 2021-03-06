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

#### **＜ログイン時＞**

Xを起動するようにしていると(ランレベル5)、ログイン画面表示時にビープ音が鳴る。

「システム(System)」→「管理(Administration)」→「ログイン画面(Login Screen)」から「アクセシビリティ(Accessibility)」タブを開き、「ログイン画面を表示した時」のチェックを外す。

ログアウトし、次にログイン画面が表示されるときには、ビープ音は鳴らないようになっているはず。

#### **＜ターミナル使用時＞**

ターミナルを使用している時にタブを使ったりすると、ビープ音が鳴ることがある。

/etc/inputrc に以下の行を追加する(コメントアウトされた状態で既に記述があるはず)

```

set bell-style none

```

ログインしなおすと、設定が有効になりビープ音は鳴らなくなる。

#### **＜vi＞**

vi(vim)を使用していると、コマンドを間違えた時などにビープ音が鳴る。

viの設定ファイル(ホームディレクトリの.vimrcや/etc/skel/.vimrcなど)に以下の行を追加する。

```

set visualbell

```

記述してからviを起動すると、ビープ音は鳴らなくなる。

#### **＜全部のビープ音を鳴らないようにする＞**

ビープ音は、pcspkrというモジュールを経由して、マザーボード上のスピーカを動かして鳴らしている。このモジュール自体をロードしないようにしてしまえば、ビープ音は鳴らない。

/etc/modprobe.d/blacklist ファイルに以下の行を追加する。

```

blacklist pcspkr

```

#### **＜VMware Playerを使用している場合＞**

VMware Playerを使用している時には、ゲストOSでビープ音が鳴るタイミングで、VMware Playerを通じて、ホストマシンのビープ音が鳴ってしまう。これを鳴らさないようにするには、VMware側の設定でビープを無効にしてしまう。

C:\Users\(ユーザ名)\AppData\Roaming\VMware\preferences.ini ファイルと、対象になる仮想マシンの設定ファイル(.vmx)に以下の行を追加する。

```

mks.noBeep = "TRUE"

```

出典 : <a href="http://kb.vmware.com/kb/1664" target="_blank">VMwareのKnowledge base</a>

#### **＜Windowsでビープ音を鳴らさないようにする＞**

ついでにWindowsの場合も。ビープ音を鳴らすドライバを無効にしてしまう。以下、Windows 7での例。

  1. コントロールパネルなどから「デバイスマネージャ」を起動
  2.  「表示」→「非表示のデバイスの表示」
  3. ツリーから「プラグアンドプレイではないドライバ」→「Beep」を右クリックし、プロパティ
  4.  「ドライバー」タブで「停止」を選択

以上

