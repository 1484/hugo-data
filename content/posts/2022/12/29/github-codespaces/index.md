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
cover: "github-codespaces.png"

archives: "2022"
toc: true
# {{< fancybox "." "photos.jpeg" "alt text" "gallery" >}}
---
## GitHub Codespacesが使えるようになった

春先からパブリックベータなどの話があり話題に時折上がっていた[GitHub Codespaces](https://github.com/codespaces)が使えるようになりました！しかも毎月60時間まで無料?!と言うことで大変気になります。これまでもiPadやChromebookなどVS Codeなどが入れられない端末上でちょっとした開発やBlogの記事を書いたり出来ないかiPad活用の観点からCloud9など様々なソリューションを試してきましたが、いよいよ決定版と言える環境が整いそうです。

### 無料でできること
60時間無料！と言うことですが、何が60時間無料なのでしょう？と言う事で詳細を確認しました。

- 2coreのvmで60時間無料
- 4coreのvmで30時間無料
- 15GB/月までのストレージが無料
  - 月15GBまでのストレージを自由に使える...では無く、Codespcaesとprebuildsなど1h単位での集計加算

1コア120時間が無料と言うことらしく2コアにした場合半分の60時間、と言うことらしいです。
ちょっとストレージがややこしいですね。15GBのストレージ領域を与えられ自由に使えるのではなく、領域に対してgh repo cloneとかで消費した領域などが加算されていく感じです。
Codespacesで管理する領域に100GBのデータなどリポジトリファイルが1時間存在していた場合、その時点で100GB利用と数えられるようです。
Codespacesで同時に管理できるCodespaceは無料プランの場合2つまでとの事なのでCodespaceで作業しては削除して別のリポジトリのCodespaceを作って作業する、なんて事も考えられますがその都度ストレージ領域が消費されていくのだと思います。

もし60時間を超えた場合でも2coreの場合1時間当たり$0.18と25円程度の様なのでとてもありがたいと感じます。まぁプライベートで個人利用なのでそうそう超えないと思いますが。

ストレージのことを考えると作業を終わった際にCodespaceを削除するのではなく停止とするのが良さそうです。

{{< fancybox "." "stop-codespace.png" "Codespaceは削除じゃなくて停止にしよう" "gallery" >}}


## 現在のHugo Blog管理
このBlogは現在 GitHubとCloudflare Pagesを活用して更改しています。記事をGitHubへpushするとCloudflare Pagesにて自動Buildが走り、記事が更改されます。
GitHubで管理することで、PCでもiPadでも記事を書けるようにしています。
### PCの場合
dockerでhugo serverを立ち上げ、ブラウザで http://localhost:1313 に接続し確認しながら記事を書いていく事が出来ます。

### iPadの場合
GitHub ClientとしてWorkingCopyを愛用しています。少し手間ですがbranchを切り、ある程度記事を書き上げたらGitHubへpushすることで、Cloudflare Pagesが Preview Buildをしてくれるので、Preview用のURLで公開前の記事をブラウザで確認しながら記事を書いていく事が出来ます。

## iPadでのBlog管理がどう変わる？
今までもCloudflare PagesのPreview Build機能を使うことで公開前のコンテンツを実際にブラウザで表示に問題が無いかなど確認しながら記事を書いていく事が出来ましたが、あくまでGitHubにpushしてからCloudflare PagesでPreview Buildが走るためpushしないとPreviewを見る事が出来ませんでした。更にはPreview用のURLを知るためにCloudflare Pagesのサイトを訪れる必要もあり、少し手間と感じる部分がありましたが、これが一変します。

{{< fancybox "." "run-docker.png" "dockerで環境構築し動かせるの素敵" "gallery" >}}

ブラウザの中で動くVScodeで記事を書きながらvmの中でdockerを動かし記事をbuildすることが出来ます。上の画像は`docker-compose up` している画像になりますが、hugo serverが動作すると右下にポップアップが表示され、`ブラウザで開く` ボタンを選択するとdockerで動いているhugo serverへブラウザでアクセスできます。

私はHugoのextentionなどを用いているのでdockerを動かす方法を用いていますが、GitHub Codespacesで用意されるvmにはすでにHugoがインストールされているので、普通にHugo Serverを動かす事が出来ます。すごい。

つまりiPadでの執筆時にもdockerでリアルタイムにbuildした記事をブラウザで確認しながら執筆していけるPCと遜色のない環境が構築できる様になったと言えます。とてもありがたいですね。

{{< fancybox "." "codespaces-docker-hugo.png" "codespacesで記事を書きながらもう1枚のブラウザでpreview" "gallery" >}}

## Webブラウザさえあれば生きていける
表題ではiPadと記載しましたがブラウザさえ動いていればOKな訳ですのでChromebookとの相性も良いでしょう。
Office Mobileなどブラウザで動くWord/Excelが出てきたりしてビックリしましたが、Web技術で全てが解決する時代がいよいよやってきた感じですね。
