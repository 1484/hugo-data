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


# USB-PD対応充電器を用意しよう
付属のACアダプタもそれなりにコンパクトではありますが当然ですけれどノートパソコン専用のACアダプタになります。旅では極力荷物はコンパクトにしたいもの、付属の充電器は自宅での充電用などにして持ち歩く際はUSB-PD対応の充電器を利用するのがオススメです。

## 必要ワット数は？
必要なワット数は「製品ガイド（機種別編）」に記載がありました。

最低でも7.5W以上が必要で、PCを利用しながら充電するのであれば45W以上が必要とのことでした。

## おすすめ充電器
PCの充電は寝るときだけ、と限定するのであればもっとコンパクトでも良いですけれど念の為を考えるとPCを利用しながら充電できる45W以上のものを用意しておくのが良いでしょう。また旅先ではPCの他にスマートフォンなども充電するでしょうから複数ポート利用できるものが良いかと思います。

そんな観点からオススメの充電器のスペックは・・・

- 65Wくらいのもの
- USB-C PD対応で2ポート以上使えるもの
- プラグが折り畳めるもの

私のイチオシはCIO社さんの [CIO NovaPort TRIO 65W GaN充電器](https://www.amazon.co.jp/NovaPort-NovaIntelligence%E6%90%AD%E8%BC%89-iPhone14-iPhone13-%E3%82%A6%E3%82%A9%E3%83%BC%E3%83%AB%E3%83%81%E3%83%A3%E3%83%BC%E3%82%B8%E3%83%A3%E3%83%BC/dp/B0B2P6HD4L?crid=3B4D682ZNI063&keywords=cio+%E5%85%85%E9%9B%BB%E5%99%A8&qid=1705836433&sprefix=CIO+%2Caps%2C241&sr=8-1-spons&sp_csd=d2lkZ2V0TmFtZT1zcF9hdGY&psc=1&linkCode=ll1&tag=kerurudigit-22&linkId=621f4bc313f4076fbc0757551fb92db0&language=ja_JP&ref_=as_li_ss_tl) です。

今どきUSB-Aポートは不要、と言う方は2ポートのものかUSB-Cのみ3ポートのものなど選択しても良いでしょう。私はPCとスマートフォンはUSB-Cで充電、AmazfitのスマートウォッチがUSB-Aで充電と言う感じなんでこの構成の充電器が便利に感じています。

