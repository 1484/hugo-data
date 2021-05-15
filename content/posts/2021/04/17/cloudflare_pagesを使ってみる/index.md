---
title: Cloudflare PagesでHUGOのコンテンツを公開
date: 2021-04-17T10:00:00+09:00
lastmod: 2021-05-15T13:00:00+09:00
draft: false
author: Keruru
# authorlink: https://author.site
categories: ["cloud"]
tags: ["hugo"]
# showcase: true
cover: "cloudflare_with_hugo.png"
---
これまで[netlify](https://www.netlify.com/)を使ってきましたが、[Cloudflare Pages](https://pages.cloudflare.com/)が公開されたと言う事で本サイトのコンテンツを移してみました。

# Cloudflare Pagesとは
CDNとして有名なCloudflare社が提供するサービスです。今はgithubだけの様ですが、githubと連携してCI/CDを回してくれてWebサイトを構築してくれます。
同様のサービスとしては[netlify](https://www.netlify.com/)や[github Actions](https://github.co.jp/features/actions) + [github Pages](https://pages.github.com/)などなど。

## Cloudflare Pagesの制約
Freeプランで使える制約は下記のとおりです。詳細は[ドキュメント](https://developers.cloudflare.com/pages/platform/limits)に記載があります。
個人ユースでは超える事は無いでしょうが、[Proプラン以上の制約はこちら](https://pages.cloudflare.com/#pricing)になります。

- Buildは月間500回まで
    - previewのbuildも出来る
- 同時にBuild出来るのは1Buildまで
- ファイル数は20,000ファイルまで
- 1ファイルあたり25MBまで
- データ転送量無制限!!

previewのbuildって何のことだろう？と思っていたら、githubでbranchを切った状態でpushすると、branchをプレビュー状態としてbuildしてbranchの内容でpreview用のURLを作ってくれると言う物でした。これ地味にすごい！って思ったのも移行を考えた１つの理由です。branch切って修正してはpushしていけば、実環境を用いてpreview出来るんですよ。しゅごい。

### preview buildはまだ微妙? いえ、最高です!!
実際に使ってみるとpreview buildにも課題が見えました。HUGOで運用していますがHUGOの設定ファイル(config.toml)に `baseURL` を入れているため確かにpreview buildのURLを作ってくれてコンテンツ変更を行ったプルリクエストの内容を含むサイトが出来るものの、`baseURL` を変更してbuildしてくれる訳では無いのでpreviewとしてはまだ使い辛いと感じました。でもそれは誤解でした。

ふと、それだったらbaseURL指定しなければ良いんじゃないの？！と思い立ってbaseURL指定を "/" に変更してみました。これでばっちり。これでbranchを用いてpreviewが出来ます。しかも先に記述している通りpreviewのbuildは無制限。もうこれはiPadのために生まれてきた機能と言っても過言ではないです。

iPadで[Working Copy](https://apps.apple.com/jp/app/working-copy-git-client/id896694807)を用いてcloneしたHugoのリポジトリでbranchを切り編集、pushします。その瞬間にプレビューのbuildが走ります。previewのbuildが完了するとcloudflare pagesのデプロイページにデプロイ単位で `ランダムな文字列.リポジトリ名.pages.dev` と言うリンクが生まれます。ここにアクセスするとプレビューが見れるんです。

### Aliasも作られるのでアクセスも楽々
デプロイ単位でランダムなリンクが生成されるわけですが、もう1つアクセスしやすいリンクが作られます。 `ブランチ名.リポジトリ名.pages.dev` と言うもの。此方でしたらランダムな文字列と違いCloudflare Pagesのデプロイページを参照しに行かなくても確認が容易ですね。

{{< fancybox "." "cloudflare_pages_deploys.png" "cloudflare Pagesのデプロイ一覧ページ" "gallery" >}}

ノートパソコンなどでHugoを用いている場合 `hugo server` コマンドを用いてlocalhost:1313にWebサーバを立ててブラウザでプレビューしながら作業する事が多いと思います。しかしiPad上ではHugoが動きませんのでどうしても想像だったりMarkdown Editorのプレビューに頼るしかありませんでした。しかしこれをcloudflare pagesで担ってもらえる事でbuildの度にcloudflare pagesのデプロイページに行ってプレビューのURLを知る必要はありますが、iPadでプレビューを見ることも全部サーバレスで出来るんです。私はVPS上でプレビュー専用のCI/CD環境を作ってましたがこれでもう要らないですね。cloudflare pagesだけで完結します。素晴らしい!!

**cloudflare pagesをHugoで使う人は可能ならbaseURLは "/" で運用するのが良いです**

# 構築のポイント
構築自体はとても簡単で[公式に手順も公開](https://developers.cloudflare.com/pages/how-to/deploy-a-hugo-site)されていますのでその通りに実施するだけでHUGOのサイトが公開できます。英語ですが難しい事書いてないので大丈夫。

忘れちゃいけない点としては公式の手順にも記載がありますが環境変数として `HUGO_VERSION` を指定することです。
下の例では `GO_VERSION` も指定してますがこちらは指定しなくて大丈夫ですが、`HUGO_VERSION` は指定しないと結構古いVersionのHUGOでbuildされてしまうのでテーマによってはBuildでエラーになる可能性があります。

{{< fancybox "." "cloudflare_hugo_env.png" "cloudflare Pagesの環境変数設定" "gallery" >}}

プロダクションとプレビューでバージョンを変える事も出来る様なので、上記の `baseURL` 問題が解決したらHUGOやGOの新バージョンでの動作確認などもメインのサイトに影響なくオンラインでテスト出来る事になります。個人サイトなのでローカルにDocker等で新バージョンの環境立てて検証するので充分ですが、複数人で運用しているサイトをお持ちの方などはとても助かる機能じゃないでしょうか。

## Failed due to an internal error
さて、ここは私がハマったポイントです。公式手順通りにサイト環境を設定、構築したのですが何度試してもログに `Failed due to an internal error` と出てdeployが失敗します。FAQなどにも特に記載もなく、仕方ないので[discord](https://discord.com/invite/cloudflaredev)で検索してみても同じように悩んでいる方が居る一方で解決の糸口は特に無く、仕方ないので様々調査をしていました。

{{< fancybox "." "cloudflare_deploy_failed.png" "cloudflare Pagesでdeploy出来ずにハマった" "gallery" >}}

----

結果としては上記のCloudflare Pagesの制約に書いてあるうちの **1ファイルあたり25MBまで** と言う制約に引っかかっているみたいでした。
HUGOのコンテンツファイルとしては上記のファイル数も1ファイル辺りのファイルサイズも要件をすべて満たしていたので **どうして？！** って感じだったのですが、まさかの.gitフォルダ以下に25MBを超えるデータがあった、と言う物でした。HUGOのデプロイとしてはpublicフォルダを指定しているので制約を受けるのはpublicフォルダ配下のコンテンツのみだろう？と考えていたのですが、まさかの.git配下のファイルがこの制約を受けるとは思いませんでした。なるほど確かに595MBのファイルがありますが **.gitフォルダを見に行くっていうのはバグじゃないの？**

```shell
keruru@kaga:~/hugo-data$ ls -lahR .git/objects/pack/
.git/objects/pack/:
total 595M
drwxr-xr-x  2 keruru keruru 4.0K Apr 17 12:59 .
drwxr-xr-x 12 keruru keruru 4.0K Apr 17 17:53 ..
-r--r--r--  1 keruru keruru 114K Apr 17 12:59 pack-########################################.idx
-r--r--r--  1 keruru keruru 595M Apr 17 12:59 pack-########################################.pack
keruru@kaga:~/hugo-data$
```

### 解決方法
どうやって解決したものか、と考えていたらやはり[公式の手順のissuesの中](https://developers.cloudflare.com/pages/platform/known-issues)にヒントがありました。

> Pages cannot serve files located in a .well-known folder.

なるほど、serveしたくないfilesは `.well-known` に書けと言う事の様です。早速リポジトリのトップディレクトリ(config.tomlと同じ階層)にファイルを作成、ファイルの中には `.git` と書いてcommitしてみました。その結果、無事deployも完了し、こうやって今このサイトはCloudflare Pagesにて公開、皆様に見ていただけています。良かった良かった。**.gitフォルダの中を読んでしまう事象** はバグに近いと思うのでこの手順を踏まなくても良くなる日は遠からず来そうな気がしますが、それまで同じ事象に陥った方は試してみて貰えたらと思います。[discordにも解決方法を書いておいたので](https://discord.com/channels/595317990191398933/789155108529111069/832888420585832468)きっとそのうち中の人が気付いて直してくれると思います。(issueの投げ方がよくわからん)

このファイルはリポジトリの量やcommit等の回数で増減するのだと思うので、必ずしも再現しない辺りが解決に時間がかかってしまっている要因かも知れませんね。
