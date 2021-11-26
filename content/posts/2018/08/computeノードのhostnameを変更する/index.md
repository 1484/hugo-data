---
title: "Computeノードのhostnameを変更する"
date: 2018-08-14T22:41:20+09:00
draft: false
slug: computeノードのhostnameを変更する
categories: ["cloud"]
tags: ["devstack", "openstack"]
cover: "OpenStack.jpg"
archives: "2018"
toc: true
---

お仕事でのこと、Multi node構成のOpenStack環境を構築後に一部compute nodeのhostnameが間違っている事が判明しました。なるほど、そんな事もあるよねぇ……なんて思いつつ、はてどうやって対処したら良いのかちょっと興味が湧きました。お仕事ではどう対処したんですかって？…それは秘密です（まだ決めてない）

ちょっと興味が湧いたのでMulti node環境を構築して試してみる事にしました。multi-node環境を構築するのは [OpenStackクラウドインテグレーション](https://amzn.to/2wb7438) の書籍で紹介されているOpenStackユーザー会が作成したscriptを用いるのが簡単です。本書で紹介されているgithubのリソースはちょっと古くて動かない事もあり、独自に [forkして修正したリポジトリ](https://github.com/1484/quick-devstack) を利用して構築します。
このscriptを用いて構築した場合は次のような構成になります。


- stack01 : controller node + compute node
- stack02 : compute node + cinder volume
- stack03 : compute node + cinder volume


書籍の手順通りに構築し終わった所でcompute node (stack03) のホスト名をstack04へ変更してみました。 

```shell
# hostnamectl set-hostname stack04
```

この状態でまずはcontroller nodeで変更されるか確認してみます。様々な確認方法があるかと思いますが、ここではhypervisor listを見てみる事にしました。

```shell
ubuntu@stack01:~/devstack$ openstack hypervisor list
+----+---------------------+-----------------+----------------+-------+
| ID | Hypervisor Hostname | Hypervisor Type | Host IP        | State |
+----+---------------------+-----------------+----------------+-------+
|  1 | stack01             | QEMU            | 192.168.100.10 | up    |
|  2 | stack02             | QEMU            | 192.168.100.11 | up    |
|  3 | stack03             | QEMU            | 192.168.100.12 | up    |
+----+---------------------+-----------------+----------------+-------+
```


この状態ではまだ適用されていません。nova-computeが起動する際に情報をcontrollerへ送るのでstack04のcompute node側でnova-computeサービスをrestartしてあげる必要があります。restartの方法はHUPを送るでも良いのでしょうけれども、まだscreenを有効にしているDevStackなので、screenを開いてn-cpu のタブでCtrl+cを送信しプロセスを終了、!! にて再起動してあげればOKです。この状態でまたcontroller nodeで確認をしてみました。

```shell
ubuntu@stack01:~/devstack$ openstack hypervisor list
+----+---------------------+-----------------+----------------+-------+
| ID | Hypervisor Hostname | Hypervisor Type | Host IP        | State |
+----+---------------------+-----------------+----------------+-------+
|  1 | stack01             | QEMU            | 192.168.100.10 | up    |
|  2 | stack02             | QEMU            | 192.168.100.11 | up    |
|  3 | stack03             | QEMU            | 192.168.100.12 | up    |
|  4 | stack04             | QEMU            | 192.168.100.12 | up    |
+----+---------------------+-----------------+----------------+-------+
ubuntu@stack01:~/devstack$
ubuntu@stack01:~/devstack$ openstack hypervisor list
+----+---------------------+-----------------+----------------+-------+
| ID | Hypervisor Hostname | Hypervisor Type | Host IP        | State |
+----+---------------------+-----------------+----------------+-------+
|  1 | stack01             | QEMU            | 192.168.100.10 | up    |
|  2 | stack02             | QEMU            | 192.168.100.11 | up    |
|  3 | stack03             | QEMU            | 192.168.100.12 | down  |
|  4 | stack04             | QEMU            | 192.168.100.12 | up    |
+----+---------------------+-----------------+----------------+-------+
ubuntu@stack01:~/devstack$ 
```

しばらくはstack03とstack04の両方がup Stateでしたが、暫くするとstack03がdown Stateに変更されました。

今回はnovaについてのみ記載しましたが様々なサービスがあるかと思いますので、ホスト名を変更した後はそれぞれのサービスを再開させるか、再起動して適用してしまえば良いかな、と思います。

はてさて、ゴミとして残ったstack03のdown Stateはどうしたら良いでしょう。様々OpenStackコマンドを探しましたが、これを削除するコマンドは特に無いようです。現状SQLデータベースを削除するしかないのかなぁ……

もしお詳しい方がいらっしゃいましたらコメント頂けると幸いです。

【2018/08/16 追記】

会社のSlackで相談してみたらプロにアドバイス頂きました。

> openstack compute service delete で行けるはず

と言う事で早速試してみましょう。まずは[ドキュメントの確認](https://docs.openstack.org/python-openstackclient/pike/cli/command-objects/compute-service.html)です。

> **compute service delete**
> Delete compute service(s)
> Compute service(s) to delete (ID only)


なるほどなるほど。指定できるのはID onlyとの事なのでIDを調べる必要がありますのでまずはlistコマンドで確認します。

```shell
ubuntu@stack01:~/devstack$ openstack compute service list
+----+------------------+---------+----------+---------+-------+----------------------------+
| ID | Binary           | Host    | Zone     | Status  | State | Updated At                 |
+----+------------------+---------+----------+---------+-------+----------------------------+
|  4 | nova-conductor   | stack01 | internal | enabled | up    | 2018-08-16T03:47:14.000000 |
|  6 | nova-scheduler   | stack01 | internal | enabled | up    | 2018-08-16T03:47:13.000000 |
|  7 | nova-consoleauth | stack01 | internal | enabled | up    | 2018-08-16T03:47:14.000000 |
|  8 | nova-compute     | stack01 | nova     | enabled | up    | 2018-08-16T03:47:07.000000 |
|  9 | nova-compute     | stack02 | az1      | enabled | up    | 2018-08-16T03:47:14.000000 |
| 10 | nova-compute     | stack03 | az2      | enabled | down  | 2018-08-14T06:24:01.000000 |
| 11 | nova-compute     | stack04 | nova     | enabled | up    | 2018-08-16T03:47:05.000000 |
+----+------------------+---------+----------+---------+-------+----------------------------+
ubuntu@stack01:~/devstack$ 
```

これで消したい情報がID 10であると確認できましたので、deleteで削除して再度listで確認してみましょう。

```shell
ubuntu@stack01:~/devstack$ openstack compute service delete 10
ubuntu@stack01:~/devstack$ 
ubuntu@stack01:~/devstack$ openstack compute service list
+----+------------------+---------+----------+---------+-------+----------------------------+
| ID | Binary           | Host    | Zone     | Status  | State | Updated At                 |
+----+------------------+---------+----------+---------+-------+----------------------------+
|  4 | nova-conductor   | stack01 | internal | enabled | up    | 2018-08-16T03:48:04.000000 |
|  6 | nova-scheduler   | stack01 | internal | enabled | up    | 2018-08-16T03:48:03.000000 |
|  7 | nova-consoleauth | stack01 | internal | enabled | up    | 2018-08-16T03:48:04.000000 |
|  8 | nova-compute     | stack01 | nova     | enabled | up    | 2018-08-16T03:48:07.000000 |
|  9 | nova-compute     | stack02 | az1      | enabled | up    | 2018-08-16T03:48:04.000000 |
| 11 | nova-compute     | stack04 | nova     | enabled | up    | 2018-08-16T03:48:05.000000 |
+----+------------------+---------+----------+---------+-------+----------------------------+
ubuntu@stack01:~/devstack$ 
```

ばっちり消えました！では最後にhypervisor listで確認してみます。

```shell
ubuntu@stack01:~/devstack$ openstack hypervisor list
+----+---------------------+-----------------+----------------+-------+
| ID | Hypervisor Hostname | Hypervisor Type | Host IP        | State |
+----+---------------------+-----------------+----------------+-------+
|  1 | stack01             | QEMU            | 192.168.100.10 | up    |
|  2 | stack02             | QEMU            | 192.168.100.11 | up    |
|  4 | stack04             | QEMU            | 192.168.100.12 | up    |
+----+---------------------+-----------------+----------------+-------+
ubuntu@stack01:~/devstack$ 
```

バッチリ期待通りの動作になっています。素晴らしい。今回はnova(compute)サービスに特化しましたが、他のサービスではどうするかなど一度ちゃんと理解しておく必要がありますね。

