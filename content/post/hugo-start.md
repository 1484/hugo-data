+++
date = "2017-09-19T00:23:55+09:00"
description = ""
title = "Hugoへの移行"
tags = ["Hugo", "WordPress", "blog", "CMS"]
+++

# Hugoにするきっかけ

WordPressで構築してきたこのサイトをHugoに移行できないか検討をはじめました。きっかけはちょっとWordPressのアップデート追っかけるのだるくなってきた事や、１人で運営している駄文に本当に動的なCMS必要？みたいな所から。

## なんでWordPressにしたんだっけ？

そもそもなんでWordPressにしたんでしたっけ？と思い起こしてみると結構WPなツールが様々あって更新が便利そう、とかそんな所だったんですが、生活を見直してみるとほんとほとんどがbashの中で生きているんですよね。あまりアプリとか使ってない。Mac使ってた頃はWordPress投稿用のツール使ったりしてたんですけれどね、そもそもそんなに記事書いてないよね（ばき

## 安心安全

最近はWordPress人気なので仕方ないですがセキュリティホールを突かれる事も多くなり、たまに0dayなものもあったりして、、、安心して運用できるStaticなサイト運営に再注目と言う感じです。でもまさか手書きHTMLももうつらいよね、と言う事でMarkdownで記述したものを変換してくれるHugoに興味が湧き始めたのでした。

# Hugo環境を作ろう！

さて、そうと決まれば作業していきましょう。と言ってもこれがなかなかそう一筋縄には行かない。どこにBuild環境を構築するか、結構悩みました。Surfaceを持ち歩くこともあるしGPD Pocketだけの事もあるし...

## ひとまず

最終的にはVPNで繋がる自宅サーバとコンテンツをrsyncするなりgithubに上げちゃうなり考えるとして、ひとまずローカルで環境を整える事に。go言語入れてHugo入れて...って所ですが何かのタイミングで動かなくなっても困るので環境はDockerコンテナに作成。　

## Preview

Hugoではserverオプションを用いる事でローカルにhttpdを上げて動作確認する事が出来ますがDockerの中で動作させてもそのままじゃ駄目よね？と言う事でコンテナを起動する時にHugoのデフォルトのポートである1313をForwardしようと

```
docker run -h hugo --name hugo -p 1313:1313 -v ~/hugo:/root/hugo -t -i hugo:current /bin/bash
```

していますがhugo serverしても見えません。はてさて？と思いながら一晩寝て起きたら簡単な事でした。ここでdockerコマンドで指定するコンテナ内部のポートはコンテナに割り当たるIPに対してのものです。**hugo serverコマンドではデフォルトではlocalhost:1313に対してサーバを立ち上げる**ので見えるわけが無いのです。

そうとわかれば簡単です。コンテナ内部でHugoを起動するオプションを変更します。

```
hugo server --baseURL="localhost" --bind="172.17.0.2" -v
```

ここで--bindのIPアドレスはこのコンテナに割り当たっている内部IPアドレスを指定します。これで無事コンテナ外のブラウザから動作確認を取る事が出来ました。よしよし。


... なんてやってたのですが、Docker内部がrootアカウントで動くためコンテンツの所有者がrootになってしまいます。まぁいちいち修正すればいいんですがそれも面倒なので早々にDockerの利用はやめました。Dockerのポートフォワードの勉強になったからまぁ良いか。