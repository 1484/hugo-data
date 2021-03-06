+++
date = "2016-01-23T16:59:32+09:00"
draft = false
title = "GoogleDocsに書いた文章をWordPressに投稿できるらしい。"
categories = ["cloud"]
tags = ["wordpress"]
cover = "WordPress-logo.png"
+++

以前Microsoft Word2007以降からはWordPressを含む様々なBlogに投稿できるようになってるんですよ、ってのを紹介しているのですがGoogleDocsでも投稿できるんだそうです。
Chromeの機能拡張なんかをウェブストアで検索していて知りました。

と言う事でこれはテストエントリーです。

もちろんMicrosoft Wordと同じく様々な装飾をかけられます。

<p><span><span style="font-weight:bold;">ボールド</span><span style="">や</span><span style="font-style:italic;">イタリック</span><span style="">や</span><span style="text-decoration:underline;">アンダーライン</span><span style="">。それぞれ当然のようにショートカットで設定できますから便利ですよね。</span></span></p><p><span><span style="">当然ですけれど画像も貼りこむ事が出来ます。きっとちゃんとアップロードしてくれるのかな。便利に使えそうな予感がちょっとだけします。</span></span></p><p></p><p><span><span style=""></span></span></p><h1><span><span style="">ちゃんとHeadingしてくれるかしら。</span></span></h1><p><span><span style="">Headingとは章項の事ですね。たとえばちゃんと解析してくれるのであればHTMLではHeaderタグが付いてくるはずです。この辺りもちゃんと処理してくれるとだいぶ楽になります。WindowsやMacやLinuxであれば好きにBlogエディタを選んだりも出来ますが例えばそれらの環境を渡り歩いたりしてたりすると面倒ですしね。何よりこの環境であればChromebookでも使えるのは大きい。ChromebookはそもそもOnline状態で使いますし管理画面にログインして直接編集していても苦にならないのですが、時折ブラウザの戻るボタンをうっかり押してしまったりして事故になったりします（汗）まぁ何かPluginを入れておけば良いのでしょうけれど、GoogleDocsで書けるならそれに越したことはありません。BlogのDBなども当然バックアップは取っていますが、Wordで一度記事を書いておく事でGoogleDriveにもバックアップが残る形になります。</span></span></p><h2><span><span style="">もちろん課題も</span></span></h2><p><span><span style="">当然ですけれど記事として投稿した後に誤字を見つけたら直接編集しにいかねばなりません。その辺りはいわゆるBlogエディタなんかとは違う弱みですね。Blogエディタでは記事の差し替えなんかもお手の物ですがこちらは「投稿」だけ。まぁ仕方ないですけれど無いよりはマシ！</span></span></p><p></p><h1><span><span style="">投稿手順</span></span></h1><p><span><span style="">まずはChromeウェブストアで「</span><a href="https://chrome.google.com/webstore/detail/docs-to-wordpress/dmmjjffhapkelioplpceelbpmpcpjeke" style="background-color:#ffffff;color:#1155cc;font-size:13pt;font-weight:bold;text-decoration:underline;">Docs to WordPress</a><span style="">」と言うPluginをインストールしましょう。</span></span></p><p><span><span style="">その後はGoogle Docsでこんな感じでガシガシ文章を書いたら「Add-ons」メニューを開きましょう。</span></span></p><p><span><span style="">おっと、まだ自分のBlogの設定してませんでしたね。</span></span>

{{< fancybox "." "0c2cae57ae1dea3ed4d60623cbeab0ca-6-1.jpg" "GoogleDocsに書いた文章をWordPressに投稿できるらしい。" "gallary" >}}

<span><span style="">Manage Wordpress Sitesを選びます。すると右に設定ペインが出てきます。</span></span></p>

{{< fancybox "." "58e65eaff86d8ce5c42db4df358f67ab-6-1.jpg" "GoogleDocsに書いた文章をWordPressに投稿できるらしい。" "gallery" >}}

<p><span><span style="">設定内容はそんなに難しい事はないので省略しますが、HTML ModeをEnhancedにすると文字装飾なども適用してくれそうです。experimentalとの事なのでとりあえず動かしてみてダメだったら戻してみるといいのかも知れません。</span></span></p><p></p><p><span><span style="">はてさて、ちゃんとこれが投稿されるのか、ちょっと楽しみです。</span></span></p><h3><span><span style="">追記</span></span></h3><p><span><span style="">無事投稿できました。おぉ？！</span></span></p>

{{< fancybox "." "040a8876e4bdd34b60feb4ca22117eb6-5-1.jpg" "GoogleDocsに書いた文章をWordPressに投稿できるらしい。" "gallery" >}}

<p><span><span style="">[ Update ]ってある！？これはもしかして文字を追記してこのボタンを押せば、追記されるのかしら！！！</span></span></p><h3><span><span style="">追記２</span></span></h3><p><span><span style="">なんとちゃんと追記されました。これは便利ですね。あとはカテゴリーやTagが設定出来ちゃったりすると最高ですね。</span></span></p><h3><span><span style="">追記３</span></span></h3><p><span><span style="">Google Docsの機能で貼り付けた画像のトリミングが出来ますが、表示上はトリミングされるんですがBlogに投稿される際には</span><span style="color:#ff0000;">トリミングした後の画像では無くて元の画像が使われてしまう</span><span style="">みたいなので注意しましょう。</span></span></p><p></p>
