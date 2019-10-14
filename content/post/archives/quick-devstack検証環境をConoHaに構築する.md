+++
date = "2016-07-09T16:13:55+09:00"
draft = false
title = "quick-devstack検証環境をConoHaに構築する"
categories = ["cloud"]
slug = "2743"
tags = ["OpenStack", "DevStack", "ConoHa"]
+++

ConoHa上にAll-in-one環境を構築出来たならこのエントリーは無くともquick-devstack環境を構築出来る方がほとんどだと思いますが、ちょっとしたメモとして残しておきます。ConoHa上にDevStack環境を構築するので<a href="https://keruru.net/2016/07/08/conoha%e4%b8%8a%e3%81%abdevstack%e7%92%b0%e5%a2%83%e3%82%92%e6%a7%8b%e7%af%89%e3%81%99%e3%82%8btips/">基本的な事項は先のエントリー</a>を参照してくださいね。ここでは<a href="https://github.com/josug-book1-materials/quick-devstack">quick-devstack環境</a>（3ノード環境）を構築する時のメモになります。

<a href="https://github.com/josug-book1-materials/quick-devstack">quick-devstack環境</a>は複数ノードを試験する際に便利な3ノード環境をDevStackで構築するときに便利な環境となります。

必要な環境の詳細はquick-devstackのREADME.mdに書いてありますが、今回は下記環境で構築しました。
ConoHaでサーバ追加画面で変更した処を記載しています。（それ以外はdefault）

<a href="/images/2016/07/quick-devstack-servers.png"><img src="/images/2016/07/quick-devstack-servers-300x144.png" alt="quick-devstack-servers" width="300" height="144" class="aligncenter size-medium wp-image-2751" /></a>
<ul>
	<li>stack01:メモリ4GB,ubuntu,14.04(64bit)</li>
	<li>stack02:メモリ4GB,ubuntu,14.04(64bit),SSD250GB</li>
	<li>stack03:メモリ4GB,ubuntu,14.04(64bit),SSD250GB</li>
</ul>

余談ですがConoHaではSSD50GBが標準ですが、stack02/03の様にSSD250GBを選択すると、vdaとして50GB割り当たりvdbとして200GBが割り当たります。stack02/03ではquick-devstackのそれぞれのフォルダのsetup.shを確認するとわかりますが、cinder-volumesとしてvdbをフォーマットします。丁度よいですね。

<!--more-->


一度この環境でサーバを構築したら全部シャットダウンして停止状態にして、ネットワークを追加します。

プライベートネットワーク作成画面ではクラスCを指定し、<strong>192.168.100.0/24</strong> のネットワークを作成します。

<a href="/images/2016/07/create-network.png"><img src="/images/2016/07/create-network-300x181.png" alt="create-network" width="300" height="181" class="aligncenter size-medium wp-image-2745" /></a>

作成しただけではVPSに接続はされませんので、右のほうにある設定ボタンを押し接続させる必要があります。

<a href="/images/2016/07/connect-nw.png"><img src="/images/2016/07/connect-nw-300x173.png" alt="connect-nw" width="300" height="173" class="aligncenter size-medium wp-image-2744" /></a>

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

これでquick-devstackの環境をConoHa上に構築出来ましたので<a  href="http://www.amazon.co.jp/gp/product/4798139785/ref=as_li_ss_tl?ie=UTF8&camp=247&creative=7399&creativeASIN=4798139785&linkCode=as2&tag=kerurudigit-22">OpenStackクラウドインテグレーション オープンソースクラウドによるサービス構築入門</a><img src="http://ir-jp.amazon-adsystem.com/e/ir?t=kerurudigit-22&l=as2&o=9&a=4798139785" width="1" height="1" border="0" alt="" style="border:none !important; margin:0px !important;" />に記載の検証手順を書籍を読みながら実施することが出来る様になります。

書籍を読むだけでなく実際に環境を触りながら学習することで学習密度が各段にアップし理解が深まりますので、実際に環境を触りながら学習することを強くおススメします。

そしてVMwareやVirtualBoxを用いて仮想サーバを3台、ご自身のPC上に構築しようとすると結構なリソースを持ったPCが無いとしんどいので、これをConoHa上に構築することが出来て時間単位の課金で利用出来るってのはとても素晴らしいことだと思います。なんといっても今回のこのサーバ1台あたり1時間動かしても8.3円、今回は3台構築してますので1時間30円程で勉強に使える計算です。もちろん使わないときはサーバを削除しておけばお金はかかりません。都度勉強する時に環境を構築しなおしになるのでちょっと手間ではありますが、それによってパソコンのスペックを問わなくなりますので、高性能パソコンを用意するより安く済むと思います。（実際Chromebookを用いて学習したりもしています）

ConoHaさんありがとう！


<iframe src="http://rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=000000&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=kerurudigit-22&o=9&p=8&l=as4&m=amazon&f=ifr&ref=ss_til&asins=4798139785" style="width:120px;height:240px;" scrolling="no" marginwidth="0" marginheight="0" frameborder="0"></iframe>

<hr />
追記、local.confファイルに<strong>ADMIN_PASSWORD</strong>という行があり、ブラウザで入る時や各種APIを使った通信などで用いるパスワードですが検証のテンポラリ環境とは言え、デフォルトでは脆弱なので変更しましょう。
