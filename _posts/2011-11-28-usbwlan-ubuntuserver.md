---
id: 900
title: Ubuntu Server 11.04マシンでUSB無線LANアダプタを使う
date: 2011-11-28T16:17:40+00:00
author: doublemarket
layout: post
guid: http://b.l0g.jp/?p=900
permalink: /linux/usbwlan-ubuntuserver/
categories:
  - Linux
---

少し前に自宅で色々と検証したりするために、<a href="http://nttxstore.jp/_II_HP13476513" target="_blank">HP ML110 G6</a>を買い、Ubuntu Server 11.04をインストールした。自宅は通信費節約のため、固定回線を引かず、外出時もそのまま使えるようにUQ WiMAXの回線を契約し、<a href="http://121ware.com/product/atermstation/product/wimax/wm3500r/" target="_blank">Aterm WM3500R</a>という無線LANルータを使っている。このため、ML110を無線LAN接続する必要がある。

近くのビックカメラで、安くて小さいUSB無線LANアダプタとしてプラネックスのGW-USNano2-Mを買ってきて、無線LANルータ経由でインターネットに接続するまでのメモ。



Ubuntu DesktopならGUIツールであれこれできるが、Serverなので全部CLIで。

## ドライバパッケージをダウンロード

GW-USNano2-?(MやG)は、RealtekのRTL8192CUという無線LANチップを使っているようなので、これに対応したドライバをダウンロード。<a href="http://www.realtek.com.tw/downloads/" target="_blank">Realtekのダウンロードページ</a>を開き、以下のようにリンクをたどる

  1. Communications Network ICs
  2. Wireless LAN ICs
  3. WLAN NIC
  4. IEEE 802.11b/g/n Single-Chip
  5. Software
  6. RTL8192CUにチェックし、goボタン
  7. 「Linux driver for Kernel 2.6.38(and earlier)」から、RTL8192CU\_linux\_v3.1.2590.20110922.zipをダウンロード

## ドライバのインストール

パッケージを展開し、コンパイル、インストール

```
  
$ unzip RTL8192CU\_linux\_v3.1.2590.20110922.zip
  
$ cd RTL8192CU\_8188CUS\_8188CE-VAU\_linux\_v3.1.2590.20110922/
  
$ sudo sh install.sh
  
```

※ Ubuntu Serverをインストールした直後は、makeやgccがインストールされないので、事前にsudo apt-get install make gccしておく。

インストールされたら、ドライバモジュールがロードされていることを確認

```
  
$ lsmod
  
Module Size Used by
  
nls_utf8 12557 0
  
isofs 40283 0
  
vesafb 13761 1
  
8192cu 508081 0 ← ロードされている
  
lp 17789 0
  
parport 46458 1 lp
  
usbhid 46956 0
  
hid 91020 1 usbhid
  
tg3 141750 0
  
```

## 無線LAN設定

WPA認証情報をファイルに書き込む。SSIDとキーは使用している無線LANアクセスポイントのものに置き換える。

```
  
$ wpa\_passphrase \[SSID\] \[キー\] > wpa\_supplicant.conf
  
$ sudo mv wpa\_supplicant.conf /etc/wpa\_supplicant/
  
$ sudo chown root:root /etc/wpa\_supplicant/wpa\_supplicant.conf
  
```

認証の設定を/etc/network/interfaceに設定を書き込む。DHCPを使用する場合の例。3行目のSSIDは上と同じく置き換えること。

```
  
auto wlan0
  
iface wlan0 inet dhcp
  
wpa-essid [SSID]
  
wpa-conf /etc/wpa\_supplicant/wpa\_supplicant.conf
  
```

マシンを再起動

```
  
$ sudo reboot
  
```

再起動後、wlan0というインタフェースができており、ネットワークに接続できればOK。iwconfigコマンドで無線インタフェースの状態を確認できる。

```
  
lo no wireless extensions.

eth0 no wireless extensions.

wlan0 IEEE 802.11bgn ESSID:"aterm-xxxxxx-g" Nickname:"<WIFI@REALTEK>"
            
Mode:Managed Frequency:2.412 GHz Access Point: xx:xx:xx:xx:xx:xx
            
Bit Rate:150 Mb/s Sensitivity:0/0
            
Retry:off RTS thr:off Fragment thr:off
            
Power Management:off
            
Link Quality=47/100 Signal level=47/100 Noise level=0/100
            
Rx invalid nwid:0 Rx invalid crypt:0 Rx invalid frag:0
            
Tx excessive retries:0 Invalid misc:0 Missed beacon:0
  
```

