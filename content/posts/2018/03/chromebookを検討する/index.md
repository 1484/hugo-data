---
title: "chromebookを検討する"
date: 2018-03-09T09:14:07+09:00
draft: false
categories: ["computer"]
tags: ["chromebook","Surface","SurfacePro4","linux"]
cover: "chromebook.jpg"
tags: ["chromebook", "Linux"]
archives: "2018"
toc: true
---

これまでSurface Pro4で生活してきましたが、勉強会など様々なシーンで **テーブルが確保できず膝上でPCを使う** と言うシーンがありました。そんな事もありSurface大好きだけれど辛いシーンが結構あるなぁ、と実感。
当時の想定だと勉強会などでもちゃんと会議机がある事が多く、昨今そのイベントも規模拡大などがあり椅子席だけの事が増えてきた、と言う経緯があるのかもしれない。イベントへの参加者が増えて盛り上がってくるのはとても嬉しい事なので仕方ないですね。

## Targetはchromebook
ヒンジがついてて膝上で快適に使えて、軽くてバッテリーも持つ端末。ってなると実はとても沢山ある。昨今のノートパソコンは大体上に当てはまる端末が殆どだろう。おねだんはちょっと頑張って14万円位で快適なものが購入できるはず、Let'sNote以外は。

## 端末の使い方を考える
一度立ち止まって考えてみる。Windowsである必要はあるのか？と。

実際Windowsが立ち上がっていても使っているのは殆どWSLばかりで、Windows端末なのにずっとBashばかり使ってる。いやいや他に使ってるアプリあるでしょ、と考えてみてもAtomにWord/Excel程度。ふむ、これだったらWindowsじゃなくてもいいな、と思い立ちました。以前にもchromebookを使っておりとても満足度が高かったのでchromebookに回帰する事を検討しました。

### Shell環境
そもそもchromebookはLinux(Gentoo)をベースに開発されており、Linuxが普通に動いている。通常使っている分にはまったく意識しないくらいなのでほんとよく出来ていると思う。でもボクが使いたいのはshell環境。一応標準で **crosh** と言うのが入ってはいます。が、これで生活できるかというと無理。
Chrome拡張でSecureShellと言うのがあるので、それを用いる事で鍵認証を用いてSSHやMosh接続が出来るので接続先で生活できます。
どうしてもLocalで欲しい場合にはCroutonなどlocalにLinux環境を構築する事も出来ます。chroot環境で動作する感じになるので、chromeOSとはデータのやり取りが面倒なので、完全にLinuxで生きていく感じになりますが、それだったらノートパソコン買ってきてUbuntuなり入れたほうが幸せかなぁと思います。

shell環境が欲しい時もlocalでAnsibleのPlaybookを書いたりyaml書いたりって事が多いですが、その実行環境をどこに持ちたいかでどんな端末が良いか決まってくるかと思います。localにDocker環境が欲しいとか思ったりもしますが欲張るとキリがないので割り切ってしまう事にしました。全部外部に持つ。あくまでノートパソコンは端末、とすると一気に端末のハードルが下がります。

### Atom
残念ながらAtomはChromebookで動きませんので代替が必要で、代替となるEditorはまだ出会えていません。ただプログラマではないのでCodeを書く事も殆ど無く、書いてもScriptやyamlやPlaybook程度なのでそれだったらAtomへのこだわりも必要ないかな、と納得させました。現状Cloud9を導入する事でこの辺は解決させてますが、Offlineでは全く使えないのでどうしたものか、まだここは検討したい所。

ただし、Markdown Editorだけは [stackedit](https://stackedit.io/) と言う素晴らしいエディタがあり、オフラインでも使えるのでとても便利です。

### Word/Excel
昨今のchromebookはAndroidアプリが使えるのでAndroid版のWord/Excel/PowerPointが使えます。office365の契約もしているので何ら問題ありません。

## chromebookで問題ない
以前chromebookを使っていた時はまだ初期の頃でandroidアプリも動きませんでしたし、OfficeもブラウザでOfficeOnlineを使うしかありませんでしたが、私の用途ならcloudサービスの助けは必要ですが問題なさそうです。
