---
title: iPadでGitHub Codespacesを活用
slug: GitHub-Codespaces
date: 2022-12-29T00:00:00+09:00
lastmod: 2022-12-29T00:00:00+09:00
draft: false
author: Keruru
# authorlink: https://author.site

# category一覧
# cloud , computer, cycle, gadget, things, trip
categories: ["computer"]

# よく使うtag
# amazon, android, conoha, chromebook, ipad, linux, network, openstack,
tags: ["ipad", "github"]
# showcase: true
imgs: "github-codespaces.png"

archives: "2022"
toc: true
# {{< fancybox "." "photos.jpeg" "alt text" "gallery" >}}
---

## GitHub Codespaces が使えるようになった

春先からパブリックベータなどの話があり話題に時折上がっていた[GitHub Codespaces](https://github.com/codespaces)が使えるようになりました！しかも毎月 60 時間まで無料?!と言うことで大変気になります。これまでも iPad や Chromebook など VS Code などが入れられない端末上でちょっとした開発や Blog の記事を書いたり出来ないか iPad 活用の観点から Cloud9 など様々なソリューションを試してきましたが、いよいよ決定版と言える環境が整いそうです。

### 無料でできること

60 時間無料！と言うことですが、何が 60 時間無料なのでしょう？と言う事で詳細を確認しました。

- 2core の vm で 60 時間無料
- 4core の vm で 30 時間無料
- 15GB/月までのストレージが無料
  - 月 15GB までのストレージを自由に使える...では無く、Codespcaes と prebuilds など 1h 単位での集計加算

1 コア 120 時間が無料と言うことらしく 2 コアにした場合半分の 60 時間、と言うことらしいです。
ちょっとストレージがややこしいですね。15GB のストレージ領域を与えられ自由に使えるのではなく、領域に対して gh repo clone とかで消費した領域などが加算されていく感じです。
Codespaces で管理する領域に 100GB のデータなどリポジトリファイルが 1 時間存在していた場合、その時点で 100GB 利用と数えられるようです。
Codespaces で同時に管理できる Codespace は無料プランの場合 2 つまでとの事なので Codespace で作業しては削除して別のリポジトリの Codespace を作って作業する、なんて事も考えられますがその都度ストレージ領域が消費されていくのだと思います。

もし 60 時間を超えた場合でも 2core の場合 1 時間当たり$0.18 と 25 円程度の様なのでとてもありがたいと感じます。まぁプライベートで個人利用なのでそうそう超えないと思いますが。

ストレージのことを考えると作業を終わった際に Codespace を削除するのではなく停止とするのが良さそうです。

{{< fancybox "." "stop-codespace.png" "Codespaceは削除じゃなくて停止にしよう" "gallery" >}}

## 現在の Hugo Blog 管理

この Blog は現在 GitHub と Cloudflare Pages を活用して更改しています。記事を GitHub へ push すると Cloudflare Pages にて自動 Build が走り、記事が公開されます。
GitHub で管理することで、PC でも iPad でも記事を書けるようにしています。

### PC の場合

docker で hugo server を立ち上げ、ブラウザで http://localhost:1313 に接続し確認しながら記事を書いていく事が出来ます。

### iPad の場合

GitHub Client として [WorkingCopy](https://apps.apple.com/jp/app/working-copy-git-client/) を愛用しています。少し手間ですが branch を切り、ある程度記事を書き上げたら GitHub へ push することで、Cloudflare Pages が Preview Build をしてくれるので、Preview 用の URL で公開前の記事をブラウザで確認しながら記事を書いていく事が出来ます。

## iPad での Blog 管理がどう変わる？

今までも Cloudflare Pages の Preview Build 機能を使うことで公開前のコンテンツを実際にブラウザで表示に問題が無いかなど確認しながら記事を書いていく事が出来ましたが、あくまで GitHub に push してから Cloudflare Pages で Preview Build が走るため push しないと Preview を見る事が出来ませんでした。更には Preview 用の URL を知るために Cloudflare Pages のサイトを訪れる必要もあり、少し手間と感じる部分がありましたが、これが一変します。

{{< fancybox "." "run-docker.png" "dockerで環境構築し動かせるの素敵" "gallery" >}}

ブラウザの中で動く VScode で記事を書きながら vm の中で docker を動かし記事を build することが出来ます。上の画像は`docker-compose up` している画像になりますが、hugo server が動作すると右下にポップアップが表示され、`ブラウザで開く` ボタンを選択すると docker で動いている hugo server へブラウザでアクセスできます。

私は Hugo の extention などを用いているので docker を動かす方法を用いていますが、GitHub Codespaces で用意される vm にはすでに Hugo がインストールされているので、普通に Hugo Server を動かす事が出来ます。すごい。

つまり iPad での執筆時にも docker でリアルタイムに build した記事をブラウザで確認しながら執筆していける PC と遜色のない環境が構築できる様になったと言えます。とてもありがたいですね。

{{< fancybox "." "codespaces-docker-hugo.png" "codespacesで記事を書きながらもう1枚のブラウザでpreview" "gallery" >}}

## Web ブラウザさえあれば生きていける

表題では iPad と記載しましたがブラウザさえ動いていれば OK な訳ですので Chromebook との相性も良いでしょう。
Office Mobile などブラウザで動く Word/Excel が出てきたりしてビックリしましたが、Web 技術で全てが解決する時代がいよいよやってきた感じですね。
