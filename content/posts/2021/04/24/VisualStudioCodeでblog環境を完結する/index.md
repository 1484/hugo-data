---
title: Visual Studio Codeでblog環境を完結する
date: 2021-04-24T10:00:00+09:00
lastmod: 2021-04-24T10:00:00+09:00
draft: false
author: Keruru
# authorlink: https://author.site
categories: ["cloud"]
tags: ["hugo"]
# showcase: true
imgs: "hugo.png"

archives: "2021"
toc: true

---
# これまではWSLのvimで記事書いてました
これまでの人生の大半はemacsと過ごしてきた私ですが、最近はvscodeにどっぷりって感じです。emacsも便利でしたけれどもう.emacsファイルにがしがしlispを書いて環境構築していくのがしんどくなってきたお年頃です。普段はPythonのスクリプトを少し書いてはdebugしたりする位ですが、そんなterminalで十分でしょ、って作業でも最近はすっかりvscodeばかり。

WindowsでHugoの記事を書く場合、Windowsのgolang環境とhugoをインストールしてpowershellのコンソールで動かして...なんて結構面倒でしたがWSLが登場して一気に楽になりました。Ubuntuが動くのでaptでgolang環境も整いますしhugoも入れられます。これが時代か！なんて思いながらWSLを起動してhugoを起動し、もう一枚terminalを開いた側でvimを用いて記事を書きつつchromeで `http://localhost:1313/` へ接続してpreview見ながら書いていく、っていう環境でした。

WSLも最初の頃は完全に独立したファイルシステムで画像データなどをWindows環境からWSL側にもっていくのが地味に面倒だったりで一旦flickrに写真をuploadしてその画像のリンクからcurl等でダウンロードするなんて手間かけてましたが、最近は `\\wsl$` でネットワークドライブとして見えてくる事を知ったので楽々です。

# HugoはDockerで動かすことにした
WSL2のUbuntuでaptでインストール出来るHugoに不満がある、とかそう言う事でも無いのですが、利用するアプリケーションでコンテナに入れて使えるものはどんどんコンテナに持っていって環境異存なく作業したいよね、って事でなるべくコンテナを使うようにしています。

今はWindowsを入れたノートパソコンですが、結構気まぐれにホストOSをLinuxに変更するので、環境ファイル等をgitに一元管理してOS入れなおした後その環境ファイルからホストOSの種別問わずにまったく同じ環境でアプリケーションが使えるのはやはり便利。

# Visual Studio Codeの環境同期
Visual Studio Codeも本当はconfigファイルなど環境設定ファイルがあってそれをgitなどで管理出来たら良いのですが残念ながらそういう仕組みはありません。ですが、Microsoftアカウントを用いて設定をCloudへ保存(onedrive?)して、他の環境でも復元（同期）することが出来るので、ホストOSをLinuxにしてもMacにしてもgitとDockerとvscodeをインストールするだけで、その他開発環境はまったく同一なもので動かせます。便利。

# Visual Studio Codeに入れている機能拡張
その他の機能拡張も入れていますが、Blog執筆環境に入れている機能拡張は次の通り。

- Browser Preview
- Remote - WSL
- Docker
- Markdown All in One

これだけ。配置は次のような感じにしています。

{{< fancybox "." "vscode-hugo-edit.png" "vscodeのレイアウト" "gallery" >}}

Docker機能拡張からHugoのコンテナを起動、中央の記事執筆画面の下で起動します。記事執筆画面の右側には **Browser Preview** を用いて記事のpreviewを。Markdown Previewでも良いのですが画像を埋め込んだ際などのPreviewとしては不足しているので、どうせだったらlocalで動かしたHugoでpreviewするのが確実です。右下ではWSLのTerminalを開いてちょっとしたファイルのコピーなどの操作をしたりします。

記事が書きあがったらvscodeなのでそのままgithubに記事をcommit,pushする事で、あとはCloudflare pagesがbuildしてコンテンツを配置してくれます。素晴らしい。

## 今後検討したいこと
すべて環境がVisual Studio Codeからアクセス出来たりとかなりスマートかつシンプルになったなとは思いますが、あまり自動化が出来ていないなと感じています。WSLの画面からvscodeを用いてMarkdownファイルを開く所から記事の編集を開始しているのですが、 `~/hugo` 配下のMarkdownファイルをvscodeで開いた事を検出した場合( `$pwd` とかで環境path取れると思うので)DockerのHugoコンテナを自動起動してBrowser Previewも自動で起動してきてくれる、なんて所まで出来るとさらにスマートなので検討したい。
