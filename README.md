# hugo-data
[keruru.net](https://keruru.net)の元データです。

# Update手順
記事の追加はブランチを切って行いましょう。自分向けのメモです。(gitに慣れてきたけどたまに忘れるので)
GithubCLIを入れていたら `gh repo view` でこのメモ見れるのでメモしとくと楽ですよ。

```
$ git branch new-branch  # new-branchと言うブランチを切る
$ git checkout new-branch # new-branchと言うブランチへ移動する
$ git branch
* new-branch  # こっちに * が付いてればOK
master
```

作業が完了したらコミットします。

```
$ git add .  # 追加した記事及び写真を追加
$ git commit -a # コミットメッセージは適当に
$ git push origin new-branch
```

これでgithubへ記事が転送されるので、github上でプルリク・マージを実施して完了。

ブランチもWebで削除出来るのでWebで削除しちゃいましょう。
