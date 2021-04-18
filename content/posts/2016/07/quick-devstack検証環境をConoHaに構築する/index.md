+++
date = "2016-07-09T16:13:55+09:00"
draft = false
title = "quick-devstack検証環境をConoHaに構築する"
categories = ["cloud"]
tags = ["OpenStack", "DevStack", "ConoHa"]
cover = "OpenStack.jpg"
+++

ConoHa上にAll-in-one環境を構築出来たならこのエントリーは無くともquick-devstack環境を構築出来る方がほとんどだと思いますが、ちょっとしたメモとして残しておきます。ConoHa上にDevStack環境を構築するので [基本的な事項は先のエントリー](/posts/2016/07/conoha上にdevstack環境を構築するtips/) を参照してくださいね。

ここでは [quick-devstack環境](https://github.com/josug-book1-materials/quick-devstack) （3ノード環境）を構築する時のメモになります。

[quick-devstack環境](https://github.com/josug-book1-materials/quick-devstack) は複数ノードを試験する際に便利な3ノード環境をDevStackで構築するときに便利な環境となります。

必要な環境の詳細はquick-devstackのREADME.mdに書いてありますが、今回は下記環境で構築しました。
ConoHaでサーバ追加画面で変更した処を記載しています。（それ以外はdefault）


{{< fancybox "." "quick-devstack-servers.png" "quick-devstack-servers" "gallery" >}}


- stack01:メモリ4GB,ubuntu,14.04(64bit)
- stack02:メモリ4GB,ubuntu,14.04(64bit),SSD250GB
- stack03:メモリ4GB,ubuntu,14.04(64bit),SSD250GB

余談ですがConoHaではSSD50GBが標準ですが、stack02/03の様にSSD250GBを選択すると、vdaとして50GB割り当たりvdbとして200GBが割り当たります。stack02/03ではquick-devstackのそれぞれのフォルダのsetup.shを確認するとわかりますが、cinder-volumesとしてvdbをフォーマットします。丁度よいですね。



一度この環境でサーバを構築したら全部シャットダウンして停止状態にして、ネットワークを追加します。

プライベートネットワーク作成画面ではクラスCを指定し、<strong>192.168.100.0/24</strong> のネットワークを作成します。

{{< fancybox "." "create-network.png" "create-network" "gallery" >}}

作成しただけではVPSに接続はされませんので、右のほうにある設定ボタンを押し接続させる必要があります。

{{< fancybox "." "connect-nw.png" "connect-nw" "gallery" >}}

これで環境としてはひとまず完成。サーバを起動してログインするとeth1が出来ていますので、そこにそれぞれ上記の画面で指定されているIPアドレスを指定します。/etc/network/interfacesに記述しておくとよいでしょう。


```
auto eth1
iface eth1 inet static
address 192.168.100.1   #stack01の場合。
network 192.168.100.0
netmask 255.255.255.0
broadcast 192.168.100.255
```

インターフェイスにIPを割り振ってpingで疎通出来る事を確認しておきましょう。
あとは基本的にはquick-devstackの手順通りですが、HOST_IPを変更します。

```
git clone https://github.com/josug-book1-materials/quick-devstack.git
cd quick-devstack
./stack01/setup.sh
cd ~/devstack
./stack.sh
```

例えばcontrollerノード(stack01)では上の様に実施しますが、最後の行のstack.shを実行する前に~/devstack/local.conf をviで編集します。stack01のlocal.confにはHOST_IPの行はありませんので追記してください。

```
#OFFLINE=True
RECLONE=True
HOST_IP=192.168.100.1
```

stack02とstack03のlocal.confには下記の様にもともと記載があります。HOST_IPにはそれぞれeth1に割り当てたIP(192.168.100.2または192.168.100.3)を、CC_HOSTにはstack01のIP(192.168.100.1)を指定します。

```
#OFFLINE=True
RECLONE=True

CC_HOST=192.168.100.1
HOST_IP=192.168.100.2
```

これで準備万端です。まずはcontroller nodeのstack01で./stack.shを実行し、無事インストールされた事を確認したらstack02とstack03の./stack.shを実行します。02と03に順序性はありませんが、インストールの途中でstack01との通信するところがありますので必ずstack01が完了してから実施しましょう。

これでquick-devstackの環境をConoHa上に構築出来ましたので<a  href="https://amzn.to/36IgJ2X">OpenStackクラウドインテグレーション オープンソースクラウドによるサービス構築入門</a>に記載の検証手順を書籍を読みながら実施することが出来る様になります。

書籍を読むだけでなく実際に環境を触りながら学習することで学習密度が各段にアップし理解が深まりますので、実際に環境を触りながら学習することを強くおススメします。

そしてVMwareやVirtualBoxを用いて仮想サーバを3台、ご自身のPC上に構築しようとすると結構なリソースを持ったPCが無いとしんどいので、これをConoHa上に構築することが出来て時間単位の課金で利用出来るってのはとても素晴らしいことだと思います。なんといっても今回のこのサーバ1台あたり1時間動かしても8.3円、今回は3台構築してますので1時間30円程で勉強に使える計算です。もちろん使わないときはサーバを削除しておけばお金はかかりません。都度勉強する時に環境を構築しなおしになるのでちょっと手間ではありますが、それによってパソコンのスペックを問わなくなりますので、高性能パソコンを用意するより安く済むと思います。（実際Chromebookを用いて学習したりもしています）

ConoHaさんありがとう！

{{< amazon asin="B00SV4V33K" title="OpenStackクラウドインテグレーション オープンソースクラウドによるサービス構築" >}}

<hr />
追記、local.confファイルに<strong>ADMIN_PASSWORD</strong>という行があり、ブラウザで入る時や各種APIを使った通信などで用いるパスワードですが検証のテンポラリ環境とは言え、デフォルトでは脆弱なので変更しましょう。
