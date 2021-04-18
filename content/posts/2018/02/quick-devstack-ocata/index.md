---
title: "quick-devstack環境をocata対応にしてみる"
date: 2018-02-25T23:07:55+09:00
draft: false
categories: ["cloud"]
tags: ["OpenStack","devstack","ocata","linux"]
slug: qucik-devstack-ocata
cover: "OpenStack.jpg"
tags: ["DevStack", "OpenStack"]
---

# OpenStackの学習といえばこの本！の内容がもう古い！

OpenStackの学習といえば、やはりまず出てくるのは日本OpenStackユーザー会の著作、 [OpenStackクラウドインテグレーション](http://amzn.to/2GIJL47) ですが、流石にもう古くてこの本の通りにはすでに構築が出来ません。
サポートサイトを通してアクセスな可能なスクリプトは [GitHubにあるのですが](https://github.com/josug-book1-materials) 2016年にメンテナンスされたのが最後。（それもとても凄いことだと思いますけれど）その際に2016年に動作させることが出来る
様にメンテナンスされたのですが、もうすでに動かない状況。OpenStackのversion upは年3回もあるので仕方ないと言えば仕方ないところではありますが。

私もかつて [ConoHaの上にこのquick-devstack環境を構築する](https://keruru.net/2016/07/09/quick-devstack%E6%A4%9C%E8%A8%BC%E7%92%B0%E5%A2%83%E3%82%92conoha%E3%81%AB%E6%A7%8B%E7%AF%89%E3%81%99%E3%82%8B/) と言う記事を書きましたが当然ですが
もうこの手順では動きません。

だからと言う訳ではないですが、一旦最新化してみようと思いました。


# TargetはOcataリリース
すぐ陳腐化するからPikeにしておけよ、と言う話もあるのですがひとまずOcataにしておきます。書籍ではOSもUbuntu14.04LTS(Trusty)をターゲットにしていますが、もはや14.04では動かないので16.04LTS(Xenial)にします。

## KVM環境による構築
ホストOSにUbuntuなりLinuxを使っていてKVMとvirt-managerが導入されている環境であれば、こちらも書籍で紹介されているスクリプトで検証環境が楽に構築できます。今回の作業にも便利なのでこちらのスクリプトもついでにupdateすることにします。

### Network Interface名が変わった
実はUbuntu15.10からNetwork Interface名がeth0などのこれまで慣れ親しんだ名前から変更になりました。やめてくれ。
当然スクリプトなどの中でeth0などの名前で指定している所もあり、どうするかなぁと思いましたが簡単なワンライナーでお茶を濁しています。
本来ならばudevの設定ファイルを書き換えてなんとかするのがベストですがちょっと今回そこまで力をかけられなかったので。

と言うことで _git cloneでubuntu-virtinstを持ってくる所_ では [こちらのリポジトリ](https://github.com/1484/ubuntu-virtinst) を用いればUbuntu16.04LTSで構築できると思います。

## quick-devstackのOcata対応
さて、VMも出来たのでOcata対応です。基本的にはlocal.confの編集がメインになりますが、その他にも一部コマンドの変更も行っています。昨今のversionではkeystoneコマンドは通らなくなっているので、openstackコマンドへ変更しました。
その他のコマンドも気になった所は変更しましたが、基本的には本書で学習する所なので対応するうちは旧コマンドのままです。本書の中で注意するとするならば *keystoneコマンドはもう使われていない* と言うことを意識する位でしょうか。

### controller nodeがうまく動かない
なるべく手を入れるのは最小限で環境差分が起きないように作りたかったのですがどうにもcontroller nodeとなるstack01がうまく作れません。仕方ないので一応学習に支障がない程度に変更してとりあえず動くようにはしました。

本当は完璧にできればプルリクもしちゃおうかと考えていましたが、あまりきれいな形に出来なかったのでプルリクは無しです。

と言うことで _git cloneでquick-devstackを持ってくる所_ では [こちらのリポジトリ](https://github.com/1484/quick-devstack) を用いると少し幸せになるかもしれません。

# トラブルを楽しみながら学習しましょう！
正直上記対応で完璧に学習が進められるかと言うと、まあ無理だと思います。トラブルが出た際には調べることも学習の近道です。ユーザー会のメーリングリストに質問を投げてみてもいいかも知れませんね。

# おまけ
今回KVMでvmを3台構築してその上に検証環境を構築しました。どの位のスペックか？と言うと ThinkPad T440sと言うノートパソコン上に構築しました。一応Core i7のCPUですがモバイル向けでメモリも12GBしかありません。ディスクも500GB程度です。
ubuntu-virtinstのscriptを見るとわかりますが、このスクリプトを作成した方はもっと良いスペックでこのスクリプトを検証されたことがわかります。スクリプトファイルのメモリサイズ等を調整することで一応上記のようなノートパソコンでも検証を
行うことが出来ます。ですがほんとギリギリの環境ですのでなるべくデスクトップPCでメモリも24GB位あると安心です。

わざわざそんな環境を構築するのもな～って方はConoHaなどクラウド上に構築するのも１つの手です。
当然プロダクト環境には使えませんが、OpenStackを学習するためだけであればConoHa,Azure上に構築することは実証済みです。
社名は明かせませんが、とあるOpenStackの研修をされている企業さんではAWS上に構築して研修生に学習環境として研修生に提供している所もありました。
毎週毎月使うものではなく月に数回、数日間程度の研修環境ですからこれこそクラウドを活用すべきって所でしょうね。

<iframe style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=000000&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=kerurudigit-22&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=4798139785&linkId=ae167602eaf198f60ff6c93b42abd060"></iframe>
