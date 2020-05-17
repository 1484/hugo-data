---
title: "AndroidからnginxのVirtualHostに繋ごうとしてハマった"
date: 2017-10-05T23:35:37+09:00
draft: false
slug: AndroidからnginxのVirtualHostに繋ごうとしてハマった
tags: ["nginx", "Linux"]
category: ["computer"]
cover: "/images/logos/linux.png"
---

# Serverを整理した

Hugoを導入してメインコンテンツをgithub Pagesに追い出した関係でConoHaに動かしていたWordPressを削除した。どうせならその他も整理しようと、だったらいっそvm作り直そうと言う事で別にインスタンスを立ち上げて移行していた。新規インスタンスに徐々にコンテンツを移行していく感じで、完了後に念のためディスクイメージを取って旧インスタンスを削除します。

PIRCやkeitairc等のscriptを移し、このサイトと別でチラ裏的に使っている日記サイトも移行をかけて、それらはFQDNを別にしてnginxのVirtualHostで動かしています。今回は新たにGitlabなんかも動かし始めました。githubじゃなく自分の環境で持っておきたいものとかはこっちにしようかと。

## 最終動作確認

さてコンテンツの移行を完了し動作確認。手元のパソコンでVirtualHostもすべて正常に動作している事を確認し寝床へ。寝床でSmartPhone(Android)から移行したコンテンツをみようとするとDefaultのnginxのページが。VirtualHostのdefault向いているコンテンツが表示されているようです。はて、VirtualHostはちゃんとPCで確認出来たしなんでスマホだけ？！とか思いつつ、DNSも弄っていたので何か悪いタイミングだったのかも？と取り敢えずその日は寝ました。

## 時間が経っても回復せず

設定を見直して見るも特に問題なく、そもそも移行前もnginxでVirtualHostを使ってちゃんと見えていた訳で。ん～～～？？

# 原因判明

そうこうしている内に気付いたのがIPアドレスの違い。PCにもAndroidにもIPv4とIPv6のアドレスが割り当たっていますが、PCではIPv4が優先になっています。ふとAndroidについて調べてみると優先はIPv6のようで。なるほど、ここまで分かれば解決は一瞬。


```
server{
    listen  80;
    listen  [::]:80;
```

VirtualHostのServerディレクティブにlistenとしてIPv4とIPv6を書いてあげます。
これで無事アクセス出来るようになりました。ちゃんとIPv6の事も考えて設定してあげないとダメですね。


