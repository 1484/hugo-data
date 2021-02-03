+++
date = "2016-07-08T01:46:05+09:00"
draft = false
title = "ConoHa上にDevStack環境を構築するTips"
categories = ["cloud"]
slug = "conoha-devstack-tips"
tags = ["OpenStack", "DevStack", "ConoHa"]
cover = "/images/logos/OpenStack.jpg"
+++

<a href="https://keruru.net/2016/04/17/conoha%e3%81%abdevstack%e7%92%b0%e5%a2%83%e3%82%92%e6%a7%8b%e7%af%89/">ConoHa上にDevStack環境を構築</a>しようとしてちょっと詰まってました。去年くらいまではConoHaではnested KVMが有効だったようで、苦なくDevStack環境を構築出来たみたいです。実際検索をしてみるとそれらしきBlogや雑誌社のBlog記事も見つかります。

しかし実際に先のエントリーの通りでDevStackのインストールは出来るものの、インスタンスを起動する事は出来ていませんでした。これはnested KVM環境では無くなった事でもうDevStack環境は構築出来なくなったものなんだろう、と勝手に思っていました。

しかし、そもそもOpenStackってlibvirtを制御してクラウドサービスっぽいものを作る基盤、って考えたらlibvirtでqemuが動くなら別にインスタンス立ち上げるのはKVMに限る必要は無いしqemuで動くんじゃないの？と思い始めました。実際nova.confの設定にはvirt_type=qemuと言う設定があります。つまりnested KVM環境では無くなった事で手軽に構築出来なくなったけど、実際動かない原因とは別なんじゃないか、と言う疑念が浮かんだので今週ちょっと時間を作ってちゃんとログを追っかける事にしました。


ちなみにDevStackのインストール自体は普通にインストールが完了します。どこでエラーとなるかと言うとインスタンスを立ち上げようとすると立ち上がらず、エラーを確認すると「利用可能なcomputeノードが無いよ(No valid host was found. There are not enough hosts available.)」と言う内容でした。/opt/stack/logs/n-cond.logを確認すると

```
 ERROR nova.scheduler.utils [req-1256d890-58f6-41b8-8013-793ada1b696e admin alt_demo] [instance: 16145ddf-99eb-4180-8a69-c8e811754297] Error from last host: devstack (node devstack): [u'Traceback (most recent call last):\n', u'  File "/opt/stack/nova/nova/compute/manager.py", line 1769, in _do_build_and_run_instance\n    filter_properties)\n', u'  File "/opt/stack/nova/nova/compute/manager.py", line 1964, in _build_and_run_instance\n    instance_uuid=instance.uuid, reason=six.text_type(e))\n', u'RescheduledException: Build of instance 16145ddf-99eb-4180-8a69-c8e811754297 was re-scheduled: Unable to get host UUID: /etc/machine-id is empty\n']
```

とあり、最後に<strong> /etc/machine-id is empty </strong>ってあるのが確認できます。

確認してみると確かにemptyでした。

/etc/machine-id が空白である問題なので、uuidgenで作成したuuidをmachine-idに書き込みます。

```
# uuidgen > /etc/machine-id
```

これでこの問題は解決出来ました。

あと注意したい所は名前解決です。ConoHaではデフォルトのホスト名はIPアドレスベースのホスト名となります。インスタンスを立ち上げっぱなしであれば変更される事は無いのでいいのですが、テスト用のマシンですし寝てる間などはインスタンスを削除しておきたい所です、お財布にも優しいですし。

ConoHaではインスタンス作成毎にIPアドレスが変わりますので、その都度原則名前解決出来るようにしておく必要があります。ついでなのでIPアドレスベースじゃないものに変更してしまいましょう。次の例はdevstackへホスト名を変更する手順となります。

```
# hostnamectl set-hostname devstack
```

変更したホスト名を /etc/hosts の 127.0.0.1 に追加しておきます。念のため ping も打って確認しましょう。

ConoHa上にDevStackではまりそうな所はこの2カ所。インスタンス削除して再作成する毎に、イメージを保存しておいてそれを読み出してのインスタンス作成においてもホスト名の疎通問題とmachine-id問題はかならず発生するので注意が必要です。

この2点を確認してみた所インスタンスの作成などが出来るようになったので、クラウドサービスを使って手軽にOpenStack環境を試してみたい、触ってみたい、って方は<a href="https://github.com/rafiror/openstack/wiki/Devstack%E5%85%A5%E9%96%80">DevStack入門</a>の手順にこの2点を追加して試してみてください。

<a href="/images/2016/07/devstack-ok.png"><img src="/images/2016/07/devstack-ok-300x184.png" alt="ConoHaでDevStackを動かしてみた。" width="300" height="184" class="aligncenter size-medium wp-image-2727" /></a>
