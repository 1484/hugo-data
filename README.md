# hugo-data
[keruru.net](https://keruru.net)の元データです。

# Update手順
記事の追加はブランチを切って行いましょう。
GithubCLIを入れていたら `gh repo view` でこのメモ見れるのでメモしとくと楽ですよ。

```shell
$ git branch new-branch  # new-branchと言うブランチを切る
$ git checkout new-branch # new-branchと言うブランチへ移動する
$ git branch
* new-branch  # こっちに * が付いてればOK
master
```

作業が完了したらコミット,プッシュします。

```shell
$ git add .  # 追加した記事及び写真を追加
$ git commit -a # コミットメッセージは適当に
$ git push origin new-branch
```

これでgithubへ記事が転送されるので、github上でプルリク・マージを実施して完了。
マージする前にcloudflare pagesを見に行くと別URLでブランチ切ったページの確認が出来ます。
本サイトにマージして問題ないか改めて確認が可能。特に新たなMarkdown文法使う時などはスマホで表示が崩れないかなど表示確認しておきましょう。

プッシュが終わったら編集に用いたbranchを削除しておきます

```shell
$ git checkout master  # masterブランチに戻る
$ git branch -d new-branch  # 編集に用いた new-branch を削除する
$ git pull
```
