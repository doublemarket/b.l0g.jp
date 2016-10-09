---
id: 1682
title: AWSのEBSボリュームのスナップショットを定期的に取る
date: 2015-10-19T13:33:30+00:00
author: doublemarket
layout: post
guid: http://b.l0g.jp/?p=1682
permalink: /aws/periodic-ebs-snapshot/
categories:
  - AWS
---

小ネタだけど、久しぶりに技術的な内容の記事を書いてみる。最近はこういうのはQiitaに書くのが流行り？

## EBSボリュームのスナップショット

インスタンスストレージ(インスタンスに無料で付いてくるローカルストレージ)と比べてEBSを使うことの利点の一つに、スナップショットを取れるというのがある。これはもちろん[AWSのコンソールからの操作](http://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/ebs-creating-snapshot.html)でも、[awscli](https://aws.amazon.com/jp/cli/)のようなツールを使ってもできるんだけど、いちいちこれらの操作をやるのは面倒になってくる。そうすると、

  * 自動で定期的にスナップショットを取りたい
  * 古いスナップショットは自動的に消したい
  * 複数のEBSボリュームのスナップショットをまとめて取りたい
  * 全ボリュームのスナップショットを取る必要はなく、一部だけ取っておきたい

みたいな話が出てくる。

そういう時にはもちろんawscliや各言語のSDKを使って自分でこれを実装してもいいのだが、[AWS Missing Tools](https://github.com/colinbjohnson/aws-missing-tools)と言う、AWSに関するちょっと痒いところに手が届くツール集があって、これに含まれるEBSスナップショット取得用のスクリプト ec2-automate-backup.sh を使うと便利。

## ec2-automate-backup.shの使い方

拡張子でわかる通り単なるシェルスクリプトで、中ではawscliを駆使して処理を行っている。なので、前提としてawscliがインストールされていて、credentialの設定が終わっている必要がある。awscliのインストール方法については割愛。

awscliの用意が済んだら、リポジトリをクローンしてきてコマンドを叩けばよい。

```
  
git clone https://github.com/colinbjohnson/aws-missing-tools.git
  
cd aws-missing-tools/ec2-automate-backup
  
```

コマンドのオプションは[README](https://github.com/colinbjohnson/aws-missing-tools/tree/master/ec2-automate-backup)に書いてあるが、イマイチわかりにくい。最低限以下がわかればとりあえずのバックアップは取れる。

  * `-s [volumeid|tag]` : バックアップ対象のボリュームをボリュームID指定するか、特定のタグが付いているもので指定するかを選択する。tagを選んでおくと、例えば「Backup=true」と付いているボリュームを一括でバックアップする、ということが可能。
  * `-v ボリュームID` : `-s volumeid`を指定した場合にボリュームIDを指定する。
  * `-t 'タグ名,Values=値'` : `-s tag`を指定した場合にタグ名を指定する。上の例のように「Backup=true」のタグが付いているボリュームをバックアップする場合、`-t 'Backup,Values=true'`と指定する。ここちょっとわかりにくい。なお、_タグはEC2インスタンスではなくEBSボリュームに付ける_。
  * `-r リージョン` : リージョン指定。ap-northeast-1とか。
  * `-n` : タグ指定でスナップショットを取った時、スナップショットのNameタグに、元のボリュームIDを入れる
  * `-k 日数` : 指定した日数以上経過したスナップショットは削除可能タグが付けられる
  * `-p` : `-k`で指定した日数以上経過したスナップショットを実際に削除する

具体的には、以下のようにコマンドを実行する。これは「Backup=trueタグが付いたボリュームのスナップショットを作成し、7日以上経過したスナップショットを削除する」例。

```
  
bash ec2-automate-backup.sh -s tag -t 'Backup,Values=true' -r ap-northeast-1 -n -k 7 -p
  
```

実際の挙動的には、`-k 7`オプションがあるので7日経つとスナップショットにPurgeAllow=trueタグが付けられ、次の実行で`-p`オプションが効いてPurgeAllow=trueがついているスナップショットが消される、という流れになる。

これをcrontabに書いておくなりJenkinsで定期実行するなりしておけば、うっかりファイルを消してしまったとか、データベースがぶっ壊れたみたいな時にもサクッと戻せて幸せになれる。

