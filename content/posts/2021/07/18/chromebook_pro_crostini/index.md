---
title: SAMSUNG Chromebook ProにLinux(Crostini)をインストールする
date: 2021-07-18T10:00:00+09:00
lastmod: 2021-07-18T10:00:00+09:00
draft: false
author: Keruru
# authorlink: https://author.site
categories: ["gadget"]
tags: ["chromebook", "linux"]
# showcase: true
cover: "crostini-hugo.png"
---

## SAMSUNG Chromebook ProにもLinux(Crostini)をインストール出来るようになった

とは言ってもたぶん半年前位には出来るようになっていたと思います。実家のPCが不調になりしばらくChromebookProを貸し出していたのと、もはやこの世代にはCrostiniは来ないのではないかなんて思っていたのでろくに追っていませんでした(笑

先月Chromebook Proが起動しないと実家からクレームが上がりASUS Chromebook CM3に更改したのでChromebook Proは引き取ってきたのでした。壊れたのかと思っていましたが実際にはUSB-CのACアダプタが壊れていただけで本体は影響なく動作。と言う事で更新ポリシーの2023年06月が迫る中ではありますがいっちょ入れてみようか、とCrostiniを入れてみる事にしました。

## SAMSUNG Chromebook ProへのCrostiniインストール方法

### まずはvm環境を有効にする

まずはChormeOSを最新にUpdateしましょう。今日(2021年7月18日)時点でdevチャンネルで ` 93.0.4554.0（Official Build）dev （64 ビット）` であった。このままでは有効にならないので設定を変更する。Chromeブラウザで `chrome://flags/#enable-experimental-kernel-vm-support` を有効(enable)にします。そして再起動。すると設定のデベロッパーにLinux開発環境が生まれます。

{{< fancybox "." "enable-vm.png" "Experimental-kernel-vmをEnableに変更する" "gallery" >}}

### インストールを開始する

再起動後に設定のデベロッパーの中にあるLinux開発環境の右にある `オンにする` ボタンを押下してインストールを開始です。

設定はLinuxユーザー名とVolumeのディスクサイズ設定くらいです。デバイスを長く使っているとキャッシュなどで有効にできるサイズが小さくなりがちです。powerwash直後はちゃんと10GBの領域を確保できるので、SAMSUNG Chromebook ProにCrostiniを入れて使おうと考えている人は一旦Powerwashしてから入れる事をおすすめします。

{{< fancybox "." "volume-setup.png" "キャッシュ等で10GB確保できない場合はpowerwashしましょう" "gallery" >}}

### packageを最新にする

インストールが完了するとターミナルが開くのでパッケージを最新にしておきます。

````
$ sudo apt update && sudo apt dist-upgrade -y
````

### 好みのアプリを入れる

好みのアプリなので好きにしていただければと思いますが、私はdockerとdocker-composeを入れました。

````
$ sudo apt install docker docker-compose
````

## Hugo blog環境

さて、このHugoのblog環境ですが最初Linuxのホームディレクトリにgit cloneして編集しようと考えていました。でもCrostiniのコンテナ環境にはそのままでは日本語入力ができません。多くの方はLinux環境側に別途anthy入れたりと日本語環境を整えているようですが、なるべくそういう無駄は排除したいと考えました。写真データ等についてもHostOSであるChromeOSからコンテナ内に配置するのが面倒でも困ります。

そこでChromebook側でフォルダを作成しそれをLinux環境にマウントして使います。

{{< fancybox "." "shared-folder.png" "ChromeOSフォルダを共有" "gallery" >}}

ChromeOS上でフォルダを作り右クリックすると、 **Linuxと共有** というメニューがありますのでそれを選択します。上記写真では共有済なのでLinuxとの共有を管理になってしまってますね。

ここで共有するとLinux側からは `/mnt/chromeos/MyFiles/` 以下にマウントされます。ここであればLinux側からもChromeOS側からもファイルを扱えるので、テキストエディタでMarkdownファイルを編集したり画像を配置したりした上で、Linux環境上でHugoを動かしてコンテンツ生成を行う事が出来ます。

{{< fancybox "." "portforward.png" "Linuxコンテナのport転送" "gallery" >}}

なお設定にあるLinux開発環境を開きポート転送の設定を行う必要があります。コンテナの1313ポートをホストOSの1313に転送しましょう。これでChromeOS上で動くChromeブラウザで `http://localhost:1313` でコンテナ内部で動くHugoサーバに接続する事が出来ます。

### リアルタイムのpreviewは出来ていないです

hugoはdockerコンテナで動かしており、-w オプションでファイルに変更があった場合にはリアルタイムに更新していく事が出来ますがおそらくChromeOS側のフォルダをマウントしている関係でdockerを動作させたタイミング等の更新までしか取得できていません。そのためエディタ側でファイル保存をかけてもリアルタイムにはコンテンツ更新が出来ませんのでdockerを立ち上げ直す感じの一手間が必要です。まぁそれでも全然快適に動くのでありがたいですが。

## text editorなかなかやるやん

これ何時頃導入されたアプリだろう。アイコンデザインがターミナルアプリに近いのでそれと同等の時期だろうか。`Text` と言うアプリがメニューにあった。せっかくなのでMarkdownファイルをこのアプリで開いてみたところMarkdownがハイライト出来てる！なかなかやるじゃない。便利。

{{< fancybox "." "text-editor.png" "Textエディタ" "gallery" >}}

1. Linuxアプリを開く
1. git cloneしてコンテンツを最新化
1. Textエディタでコンテンツを更新
    1. Textエディタで編集したり
    1. 画像ファイルを配置したり
1. Linuxアプリ上でdocker-compose up してpreview
1. git add / git push してコンテンツを公開

こんな感じの流れでコンテンツ更新が完了します。これまで外部サーバの力を借りたり面倒な手間が多かったですが、普通に操作ができる所まで来ました。快適です。

## Termuxでも出来たけど...

Chromebook上でLinux動かしてHugoコンテンツを更新するのはTermuxとJota+エディタを組み合わせる事でTermux内だけで完結する形で何となく出来たけれど、やはり画像の操作などChromeOSがちゃんと使えるのはとても便利なのでCrostini環境でHugoコンテンツ編集環境を整えられるのは便利ですね。

dockerも動くと言う事でその他様々な開発環境も構築できそうだけれどディスク容量もそんなにないですしメモリも4GBしかないのであまり欲張った環境は構築できません。Hugo環境であったりkindle環境であったり日常生活で用いるdockerコンテナを利用する程度に留まりそうです。

Linuxを日常的に使える人だと軽量IAノートPCにUbuntuでも入れたほうが快適ですがインスタントテザリングなどChromebookならではの機能を考えると、もう少しリッチなChromebookが欲しくなりますね。

