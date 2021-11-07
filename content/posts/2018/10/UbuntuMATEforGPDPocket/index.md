---
title: "UbuntuMATE for GPDPocket"
date: 2018-10-25T12:55:26+09:00
slug: UbuntuMATEforGPDPocket
tags: ["GPDPocket", "Ubuntu"]
categories: ["gadget"]
cover: "UbuntuMATEforGPDPocket.jpg"
archives: "2018"
toc: true

---

**UbuntuMATEがGPD Pocket用のインストールisoを配ってるですって?!**

GPD PocketはWindows版とUbuntu版が発売されました。Ubuntu版の方が発売が遅れ、やきもきした方も多いかと思います。私はどちらでも良かったのですが、Windowsは買わないと付いてこないがUbuntuは別にダウンロードして自分で入れればいいやとWindows版を買った口です。

そんな感じなのでGPD Pocket2ではLinux版が出ないな、なんて事はまったく気にしていなかったのですが、まさかのディストリビューション側からGPD Pocketをサポートする動きがありました。そのディストリビューションとは[UbuntuMATE](https://ubuntu-mate.org/)（マテと読みます、メイトではないです）

これまでGPD PocketにはUbuntuであれば16.04LTSを入れる事が多かったと思います。確か公式のLinux版のファームウェアでも16.04LTSだったと思います。最近になって17.xxも入るようになってきましたが結構苦労してました。しかし今回公開された UbuntuMATE for GPD Pocket/Pocket2 はUbuntu18.10ベース。最新リリースです。LTSでは無いので2019年07月にはアップデートが提供されなくなりそれまでにアップグレードが必要となるなど少し手間はかかりますが、なんと言ってもUbuntu18.10ベースと言う事でUSB Tyep-Cのサポートが入り今後USB Type-Cの様々なデバイスが使えるようになる事が期待されます。これはうれしい。手持ちのUSB Type-C -> HDMIのアダプタはまだ使えませんでしたが、今後サポートされていく可能性があるということ。楽しみです。

早速、GPD PocketにUbuntuMATEをインストールしてみました。
[ダウンロードページ](https://ubuntu-mate.org/download/)に行くと、64bit,32bit,Raspberry piに並んでGPD Pocketがあります。素敵ですね。

# インストールは簡単

ダウンロードしたisoイメージをUSBにddを用いて焼いてBIOS画面からUSBブート、その後は普通にLinuxをインストールする手順となんら変わりません。これまでも様々な方がGPD Pocket用のインストールイメージを用意して下さっていて、それらを用いてインストールする時と変わりません。

## 起動時の不具合

私の端末固有かもしれませんが、起動時にGPDロゴが出てグレーになった後にブラックアウト、そのまま上がってこないと言う状況に陥る事がままありました。電源ボタンを押すとちゃんとシャットダウンされます。どうもGRUB周りでディスプレイ周りがうまく扱えていない様です。起動はちゃんとしている様ですし、GRUBのsplash周りだと考え次の設定をして回避しています。そのうち正式に直るでしょうけれど。

### 2019/10/14現在

起動時におかしくなる現象は解消されているので普通に導入できると思います。

## GRUBの設定を変更する

起動時にUbuntuMATEのロゴが出ますが裏ではLinuxの起動がちゃんと動いています。このスプラッシュ画面を使わなければ起動時の不具合は回避できそうだったのでGRUBの設定を変更します。

vi で /etc/default/grub を編集します。

```
$ sudo vi /etc/default/grub
```

編集する箇所は、GRUB_CMDLINE_LINUX_DEFAULT="quiet splash" の行をコメントアウトするだけです。コメントアウトするだけで問題無い筈ですが私は念の為

```
GRUB_CMDLINE_LINUX_DEFAULT=""
```

とあえて設定しました。
編集が終わったらupdate-grubでgrubを更新して完了です。

```
$ sudo update-grub
```

これで次から起動する際にスプラッシュ画面が表示されずLinux起動画面が表示されます（一瞬ですが）

# 暫くはUbuntuMATEを使ってみます

メーカーサポート以上にディストリビューションサポートはとても嬉しいです。不具合などフィードバックしてより良い環境となる様に心掛けたいなと思います。Pocket2は見送りかな、と思ってたけどこうなってくると欲しくなってくるなぁ...

今後はGPD PocketでLinuxを使う方のスタンダードがUbuntuからUbuntuMATEに移っていきそうですね。これを期にLinuxを愉しむ人が増えるといいなと思います。


