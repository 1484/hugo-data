---
title: ChromeOS flexを活用してLTE対応軽量chromebookを作る
slug: chromeOSflex-LTE-mobile
date: 2024-01-21T00:00:00+09:00
lastmod: 2024-01-21T00:00:00+09:00
draft: true
author: Keruru
# authorlink: https://author.site

# category一覧
# cloud , computer, cycle, gadget, things, trip
categories: ["computer"]

# よく使うtag
# amazon, android, conoha, chromebook, ipad, linux, network, openstack,
tags: ["chromebook", "linux"]
# showcase: true
imgs: "linux.png"

archives: "2024"
toc: true
# {{< fancybox "." "photos.jpeg" "alt text" "gallery" >}}
---

軽量かつバッテリーが持つchromebookが無い！これまでに1kgを切った端末は下記の4台のみじゃないでしょうか。

- ASUS Chromebook Flip C101PA
- ASUS Chromebook C223NA
- ASUS Chromebook Detachable CM3
- Lenovo IdeaPad Duet Chromebook

そしてこの中に今持ち歩いて使いたいと思える端末が無い。WindowsPCとしては1kgを切る端末はそれなりに発売されているのですが、Chromebookの方向性が文教向けメインになったことが起因しているかは判りませんが  望むような端末が発売されません。かなしみ。Chromebook Plusの方向性は高スペックですが予算がかけられるようになった筈なので今後軽量モバイル端末も出てくれると嬉しいなと思っています。

## 今は軽量モバイルノートPCにLinuxをインストールして利用

今現在は望む形の端末が無いので中古WindowsPCを購入してLinuxをインストールして使おうと用意しました。用意したのは法人向け端末の中古品で富士通のLIFEBOOK U7310/Eです。

スペックは下記の通り、第10世代i5プロセッサに8GBメモリとそこそこ快適に動作する環境です。

[FMVU32017](https://jp.fujitsu.com/platform/pc/product/lifebook/2010/u9310e/spec.html)
- CPU : Intel 第10世代 Core i5 10310U
- Memory : 8.0GB
- Storage : NVMe接続 128GB SSD
- WiFi : WiFi6対応
- WWAN : LTE対応(Sierra Wireless, Inc. EM7430)
- 生体認証 : WindowsHello対応赤外線カメラ、指紋認証センサー (Linuxでは使えていません)
- Battery : 大容量バッテリー(中古なので若干容量減だがそれでも13時間くらいは動く)
- 質量 : 918g

OSはUbuntuを入れて使っていますが、FedoraやopenSUSEでも問題なく利用できることを確認済みです。そして何が良かったかというとLinuxでもLTEが普通に使えます。これまではLTEモデムドライバはWindowsのみだったりして仕方なくWindowsを使っていたりしましたが、Linuxでも使えるので最強の端末かなって感じています。


## ChromeOS flexを試してみようと思ったきっかけ
ChromeOS flexもGentoo LinuxをベースとしたOSです、それであればWWANドライバもあると思うしChromebook化してもWWAN使えるのでは？問題なく動作したら日常でLinuxを使うのは不安だけれどChromebookなら！って言うユーザーには最強の端末になるんじゃない？なんて思ったので試してみることにしました。

# インストール手順
インストールには8GBくらいのUSBメモリを用いますので用意します。この手順ではWindowsでインストールメディアを作りたいと思います。MacOSでも可能ですのでMacしか手元にない方は公式サイトなどを参考に作成してください。

## インストールメディアを作る
