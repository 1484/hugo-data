+++
date = "2016-04-17T01:22:33+09:00"
draft = false
title = "DevStackを会社のProxy環境下でも構築したい"
categories = ["cloud", "computer"]
tags = ["OpenStack", "DevStack"]
cover = "OpenStack.jpg"
+++

自宅での検証環境としては愛用のThinkPadにVMware Workstationを入れてその上でUbuntuを動かしそこでDevStack環境を構築しています。とても快適です。しかしあくまで個人の環境なので会社で仕事中に検証として使えません。会社ではちゃんと会社のNWで使える検証環境を構築せねばなりません。

会社の環境と自宅の環境で大きく違うこと、会社によって全然ここは異なりますのでこのエントリーで完璧とはならないかと思いますがここはあくまでそんな環境もあるよね、という話です。自宅のルータ下でも会社のNWでもNAT環境で端末に割り当てられるIPアドレスはプライベートIPである事がほとんどかと思います。そこまでは一緒ですが、会社の場合はProxyの設定がされる事が殆どです。自宅の場合はルータでNAT変換されるだけですが会社ではhttp/httpsの通信はproxy環境にありcacheを用いた通信量の低減だったり内容の検閲といった事をされている事が多いです。またproxy利用に認証が設定されており、認証情報を持っている方のみが使えるようになっていたり（たとえば社員は使えるけれどアルバイトには利用を制限させるとか）、そもそも認証する事でたとえばエッチなサイトを見に行ったIDは誰かとかがすぐに判別が出来るなどの用途もあり、proxyとセットで認証されている事が多いです。


さてそんな会社のパソコンにDevStack環境を構築するとなると結構大変です。DevStackでは基本的にscriptの中で構築に必要な資材をダウンロードしてきます。さらにそのダウンロード方法が何種類かあるのです。DevStackが構築の中で用いる通信は以下の通りです。

 - apt
 - git
 - curl

これらがちゃんとproxyを通して通信するようにしてあげる必要があります。そのための設定はいくつか必要となります。

まずはUbuntuを入れた後にそもそもapt-get upgradeとかしますよね。gitもaptでインストールする必要があります。そのための設定をしましょう。Ubuntuのインストーラーの中でproxyの設定を聞いてきますのでそこで設定していれば問題ありませんが念のため、~/.bashrcに以下を追加しておきましょう。

 
{{< highlight bash >}}

export http_proxy="http://<proxyのユーザID>:<proxyのパスワード>@<proxyのIPやFQDN>:<port 番号(指定がある場合)>"
export https_proxy="http://<proxyのユーザID>:<proxyのパスワード>@<proxyのIPやFQDN>:<port 番号(指定がある場合)>"

{{< / highlight >}}

設定をしたらログアウトしてログインし直すかsource ~/.bashrcで読み込みましょう。apt-getが使えるようになる筈です。

gitをインストールしたらgitでもproxyが使える様に設定しましょう。またgit://という様にhttpでなくgitのスキームで通信するように設定が書いてある場合があります。これはhttpで通信するようにしないといけませんね。これらはコマンドで行えます。

 
{{< highlight bash>}}

$ git config --global http.proxy http://<proxyのユーザID>:<proxyのパスワード>@<proxyのIPやFQDN>:<port 番号(指定がある場合)>
$ git config --global https.proxy http://<proxyのユーザID>:<proxyのパスワード>@<proxyのIPやFQDN>:<port 番号(指定がある場合)>
$ git config --global url.http://github.com/.insteadOf git://github.com/
$ git config --global url.http://git.openstack.org.insteadOf git://git.openstack.org/

{{< / highlight >}}

これでgitの設定も完璧です。最後にcurlです。curlは~/.curlrcというファイルを読み込みますので設定しましょう。

 
{{< highlight bash >}}

proxy-user = "<proxyのユーザID>:<proxy のパスワード>"
proxy = "http://<proxyのIPやFQDN>:<port 番号(指定がある場合)>"

{{< / highlight >}}

これでばっちりです。ここまで設定出来れば万全。

{{< highlight bash >}}

$ git clone https://git.openstack.org/openstack-dev/devstack
$ cd devstack; ./stack.sh

{{< / highlight >}}

これでproxy環境下でもdevstack環境が構築出来ると思います。
注意点としては構築後にブラウザで接続したりすると思いますがproxy設定のままだと設定次第ですがproxyに問い合わせにいってしまってローカルの仮想マシンに繋がらなかったりする事もあると思いますので要確認です。ここはほんと環境によってしまうかと思いますので頑張ってください。(私の場合VirtualBoxのポートフォワーディング設定を使ってホストPCのポートに割り当てて通信してます。127.0.0.1に対してproxy設定されている事も少ないですし不具合になりにくい)

またstack.shの中でproxyの設定も表示されてしまいますし、知っている方には上記設定ファイルを覗かれる事でアカウント漏洩になりますので、運用にはご注意を。

enjoy! devstack.
